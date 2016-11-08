---
title: VNPY by VN.PY (Part I)
tags: [quant_trading, quant_api]
keywords: quant trading, python wrapper, quant api
summary: "VNPY is a Python-based open source quant trading platform. The methodology of Python API wrapper, and its design of event-based engine will be discussed in detail in this first part."
sidebar: quant_trading_sidebar
permalink: quant_trading_vnpy_1.html
folder: quant_trading
last_updated: Oct 31, 2016
---

## About VNPY

`VNPY` is a **Python**-based open source quant trading platform. It is mainly designed for medium or small-sized quant team, professional trader team, and programmers who are interested in quant trading. It features in rapid development using dynamic programming language. For instance, it uses `boost.Python` to wrap native C++ API, uses `PyQt` to design the GUI, and `numpy` to process time series, etc. Though it's relatively slow compared with other native C++ applications, the latency can be ignored for nearly 90% strategies. The breakdown of VNPY lies in the `GIL` (global interpreter lock), which is a mutex that prevents multiple native threads from executing Python codes at once. On the other hand, it requires more work to reconstruct since Python doesn't support static type checking.

{% include note.html content="**CTP-like** API wrapper will be discussed here in detail. Most of the CTP-like APIs, including **CTP** of SFIT, FEMAS of CFFEX, **LTS** of HBSC, X-Speed of DFITC, KSFT of SUNGARD and UFT of HangSeng, have been integrated in VNPY. Some of those native APIs have been discussed briefly in separate articles." %}

## CTP-like API Wrapper

Take [LTS API by HwaBao Securities](quant_trading_lts_api.html) as an example. Once wrapped and bundle with Python (say `vnltsmd.pyd`), the workflow can be summarized in the following steps:

1. **wrapped call functions** are called in Python code (@params: PyObject)
  * PyObjects are converted to C++ objects in wrapped API
  * **native call function** is triggered in wrapped API (@params: C++ objects)
2. trader server returns data via **native callback function**
3. Match and handle certain task in wrapped API
  * C++ objects are converted into PyObjects
  * **wrapped callback function** is triggered in Python code (@params: PyObject)

{% include tip.html content="Class `Spi`(for callback functions) and `Api`(for call functions) are usually integrated into one class in most API wrapper designs." %}

{% include important.html content="Callback function in native API should return immediately once triggered. The best solution is the Producer/Consumer model, which implements a **task queue** in its programming interface." %}

### API Wrapper Specifications

Above two features can be seen from the class design. Besides, call function `ReqUserLogin()` and callback function `OnRspUserLogin()` are discussed here to cover one complete workflow mentioned above.

{% highlight C++ linenos %}
/// @file:  vnlstmd.h
/// @brief: API wrapper specification for LTS API

#include "SecurityFtdcMdApi.h"
#include <boost/python.hpp>
using namespace boost::python;

class PyLock { ... };           // get GIL lock in C++ thread
struct Task { ... };            // struct of taks
template<typename Data>         // thread-safe task queue
class ConcurrentQueue { ... };

/// @brief: class derived from CSecurityFtdcMdSpi
/// @note:  class CSecurityFtdcMdSpi and CSecurityFtdcMdApi are integrated
class MdApi : public CSecurityFtdcMdSpi
{
private:
  CSecurityFtdcMdApi* api;          // API instance
  thread *task_thread;              // boost thread pointer
  ConcurrentQueue<Task> task_queue; // task queue

public:
  MdApi()
  {
    function0<void> f = boost::bind(&MdApi::processTask, this);
    thread t(f);
    this->task_thread = &t;
  };
  ~MdApi()
  {
  };

  /// Step 1: wrapped call function is called from Python code
  /// @params: PyObject dictionary
  /// @brief:
  ///    convert to C++ object
  ///    call native call function ( ReqUserLogin() )
  int reqUserLogin( dict req, int nRequestID );

  /// Step 2: native callback function is triggered
  /// @brief:
  ///    create a task instance and push it into task queue
  virtual void OnRspUserLogin(
    CSecurityFtdcRspUserLoginField *pRspUserLogin,
    CSecurityFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast);

  /// @brief: check and pop task in the queue
  ///         match it with certain task process function
  void processTask();

  /// Step 3: certain task is called in wrapped API
  /// @brief:
  ///    convert C++ object to PyObject
  ///    call wrapped callback function ( onRspUserLogin() )
  void processRspUserLogin(Task task);
  virtual void onRspUserLogin(dict data, dict error, int id, bool last) {};
  ...
}
{% endhighlight %}

### API Wrapper Implementations

{% highlight C++ linenos %}
/// @file:  vnlstmd.cpp
/// @brief: API wrapper implementation for LTS API

#include "vnltsmd.h"

/// Step 1
int MdApi::reqUserLogin(dict req, int nRequestID)
{
  CSecurityFtdcReqUserLoginField myreq = CSecurityFtdcReqUserLoginField();
  memset(&myreq, 0, sizeof(myreq));
  getChar(req, "BrokerID", myreq.BrokerID);          // convert to C++ objects
  ...
  int i = this->api->ReqUserLogin(&myreq, nRequestID); // native call function
  return i;
};

/// Step 2
void MdApi::OnRspUserLogin(
  CSecurityFtdcRspUserLoginField *pRspUserLogin,
  CSecurityFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast)
{
  Task task = Task();
  task.task_name = ONRSPUSERLOGIN;
  ...
  this->task_queue.push(task);    // push a new task
};

void MdApi::processTask()
{
  while (1) {
    Task task = this->task_queue.wait_and_pop();  // wait or pop new task
    switch (task.task_name) {
      case ONRSPUSERLOGIN: {
        this->processRspUserLogin(task);          // match and call
        break;
      }
      ...
    };
  }
};

/// Step 3
void MdApi::processRspUserLogin(Task task)
{
  PyLock lock;
  CSecurityFtdcRspUserLoginField task_data =
    any_cast<CSecurityFtdcRspUserLoginField>(task.task_data);
  dict data;
  data["BrokerID"] = task_data.BrokerID;  // convert to PyObjects
  ...
  this->onRspUserLogin(data, error, task.task_id, task.task_last);
  // wrapped callback function is triggered in Python code
};

/// Boost.Python Wrapper
struct MdApiWrap : MdApi, wrapper < MdApi >
{
  virtual void onRspUserLogin(dict data, dict error, int id, bool last) {
    try {
      this->get_override("onRspUserLogin")(data, error, id, last);
    }
    catch (error_already_set const &) {
      PyErr_Print();
    }
  };
  ...
};

BOOST_PYTHON_MODULE(vnltsmd)
{
  PyEval_InitThreads();
  class_<MdApiWrap, boost::noncopyable>("MdApi")
    .def("reqUserLogin", &MdApiWrap::reqUserLogin)
    .def("onRspUserLogin", pure_virtual(&MdApiWrap::onRspUserLogin))
    ...
  ;
};
{% endhighlight %}

### Python Test Code

Assume **Boost C++ Libraries** are imported and above API wrapper is compiled and linked into a Python dynamic library(`vnltsmd.pyd`). Now in the main Python code, we can call the wrapped call function, and implement wrapped callback function.

{% highlight Python linenos %}
# @file: vnlstmdtest.py
from vnltsmd import *

class TestMdApi(MdApi):
  def __init__(self):
    """Constructor"""
    super(TestMdApi, self).__init__()
  def onRspUserLogin(self, data, error, n, last):
    """User Login Response"""
    print_dict(data)
    print_dict(error)
  ...

def main():
  api = TestMdApi()
  api.createFtdcMdApi('')
  api.registerFront("...")
  api.init()

  # Login
  loginReq = {}
  loginReq['UserID'] = ''
  loginReq['Password'] = ''
  loginReq['BrokerID'] = ''
  i = api.reqUserLogin(loginReq, 1)
  sleep(0.5)
  i = api.exit()

if __name__ == '__main__':
  main()
{% endhighlight %}

## Python-based Event-driven Engine

**Event-driven** model is an application of **Observer Pattern** in design pattern, which is also known as Publish/Subscribe Pattern. In this model, objects that send out events are called event sources. Listener, with its certain event type and event process, is registered to the event engine. Once the source sends out that type of events, the event process of the listener is then triggered. Event-driven model can be found in most GUI interface.

In **VNPY**, it is implemented as below. `register(self, type_, handler)`<span class="label label-default">#41</span>method is used to register a listener of its certain event type ( `type_` ) and function in response ( `handler` ) into the event engine<span class="label label-default">#43, 46</span>. When event engine runs ( `ee.start()` ), the process thread starts ( `self.__thread.start()`<span class="label label-default">#33</span>) and gets the new events in the event queue ( `self.__queue.get()` <span class="label label-default">#18</span>). When the event source (instance of class `Event`<span class="label label-default">#63</span>) sends out an event of that event type ( `put(self, event)`<span class="label label-default">#60</span>), it is then retrieved and processed ( `__process(self, event)`<span class="label label-default">#23</span>) by triggering the registered function of the listener ( `handler(event)`<span class="label label-default">#25</span>).

{% include tip.html content="`__handlers` is a dictionary in which `<key>` is the event type (`type_`), and `<value>` is a list of process functions. `__timer`, QTimer object in PyQT, sends out an event with the type of `EVENT_TIMER` per second.<span class=\"label label-default\">#28, 34</span>" %}

{% highlight Python linenos %}
# @file: eventEngine.py
# @brief: event process engine

from eventType import *

class EventEngine:
  def __init__(self):
    self.__queue = Queue()
    self.__active = False
    self.__thread = Thread(target = self.__run)
    self.__timer = QTimer()
    self.__timer.timeout.connect(self.__onTimer)
    self.__handlers = {}

  def __run(self):
    while self.__active == True:
      try:
        event = self.__queue.get(block = True, timeout = 1)
        self.__process(event)
      except Empty:
        pass

  def __process(self, event):
    if event.type_ in self.__handlers:
      [handler(event) for handler in self.__handlers[event.type_]]

  def __onTimer(self):
    event = Event(type_=EVENT_TIMER)
    self.put(event)

  def start(self):
    self.__active = True
    self.__thread.start()
    self.__timer.start(1000)

  def stop(self):
    self.__active = False
    self.__timer.stop()
    self.__thread.join()

  def register(self, type_, handler):
    try:
      handlerList = self.__handlers[type_]
    except KeyError:
      handlerList = []
      self.__handlers[type_] = handlerList
    if handler not in handlerList:
      handlerList.append(handler)

  def unregister(self, type_, handler):
    try:
      handlerList = self.__handlers[type_]
      if handler in handlerList:
        handlerList.remove(handler)
      if not handlerList:
        del self.__handlers[type_]
    except KeyError:
      pass

  def put(self, event):
    self.__queue.put(event)

class Event:

  def __init__(self, type_=None):
    self.type_ = type_
    self.dict_ = {}
{% endhighlight %}

{% include links.html %}
