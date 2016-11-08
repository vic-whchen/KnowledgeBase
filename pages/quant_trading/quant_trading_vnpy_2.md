---
title: VNPY by VN.PY (Part II)
tags: [quant_trading, quant_api]
keywords: quant trading, python wrapper, quant api
summary: "VNPY is a Python-based open source quant trading platform. In this second part, a complete VNPY trading system with its three-layer architecture will be discussed in detail."
sidebar: quant_trading_sidebar
permalink: quant_trading_vnpy_2.html
folder: quant_trading
last_updated: Oct 31, 2016
---

## Three-Layer Architecture

Different from the original `VNYP` tutorial, the three-layer architecture in `VNPY` is investigated in a top-to-bottom way, from which the caller and callee functions can be seen much clearly. Above that is the main entry of the program, which is listed below first.

{% highlight python linenos %}
# @file:  demoMain.py
# @brief: main entry of trading system

from demoEngine import MainEngine
from demoUi import *

def main():
  app = QtGui.QApplication(sys.argv)
  me = MainEngine()          # create a MainEngine in middle layer
  mw = MainWindow(me.ee, me) # bind MainEngine and its EventEngine in top layer
  mw.showMaximized()
  sys.exit(app.exec_())

if __name__ == '__main__':
  main()

{% endhighlight %}

In the main program, the `MainWindow` (in top GUI layer) is created<span class="label label-default">#10</span>and bind with the `MainEngine` (in middle layer) and its `EventEngine`<span class="label label-default">#9</span>. In the middle layer, the `EventEngine` is initialized and bind with quotation/trader API instance, and gets started then. It also provides simplified interface to the bottom layer. In the lowest layer, callback functions of `MdApi`/`TdApi` are implemented, and every single event is put into the `EventEngine` with specific callback function.

### Top GUI Layer

On creating each module (e.g. `QTableWidget`<span class="label label-default">#7, 21</span>, `QMainWindow`<span class="label label-default">#35</span>, `QDialog`<span class="label label-default">#66</span>), `initUi()` is called to set the GUI, and then `registerEvent()` is called to register the event. In each `registerEvent()`, via **signal/slot** mechanism, specific function as a slot is connected to the signal, which is then register to the `eventEngine` with a certain event type<span class="label label-default">#17, 31, 56, 57</span>.

{% include important.html content="Event handler must be connected to a **signal** (created in the class constructor<span class=\"label label-default\">#8, 22, 37, 38</span>). On registering the event, it must be replaced by its `signal.emit` method." %}

{% highlight python linenos %}
# @file:  demoUI.py
# @brief: top UI layer of the trading system
#         call main engine in the middle layer

from eventEngine import *

class LogMonitor(QtGui.QTableWidget):
  signal = QtCore.pyqtSignal(type(Event()))
  def __init__(self, eventEngine, parent=None):
    ...
    self.initUi()
    self.registerEvent()
  def initUi(self):
    ...
  def registerEvent(self):
    self.signal.connect(self.updateLog)
    self.__eventEngine.register(EVENT_LOG, self.signal.emit)
  def updateLog(self, event):
    ...

class AccountMonitor(QtGui.QTableWidget):
  signal = QtCore.pyqtSignal(type(Event()))
  def __init__(self, eventEngine, parent=None):
    ...
    self.initUi()
    self.registerEvent()
  def initUi(self):
    ...
  def registerEvent(self):
    self.signal.connect(self.updateAccount)
    self.__eventEngine.register(EVENT_ACCOUNT, self.signal.emit)
  def updateAccount(self, event):
    ...

class MainWindow(QtGui.QMainWindow):

  signalInvestor = QtCore.pyqtSignal(type(Event()))
  signalLog = QtCore.pyqtSignal(type(Event()))

  def __init__(self, eventEngine, mainEngine):
    super(MainWindow, self).__init__()
    self.__eventEngine = eventEngine    # set eventEngine in top layer
    self.__mainEngine = mainEngine      # set mainEngine in top layer
    self.initUi()
    self.registerEvent()                # register event for MainWindow

  def initUi(self):
    ...
    actionLogin = QtGui.QAction(u'Login', self)
    actionLogin.triggered.connect(self.openLoginWidget)

  def registerEvent(self):
    self.signalInvestor.connect(self.updateInvestor)
    self.signalLog.connect(self.updateLog)
    # register listener handler to eventEngine
    self.__eventEngine.register(EVENT_INVESTOR, self.signalInvestor.emit)
    self.__eventEngine.register(EVENT_LOG, self.signalLog.emit)

  def openLoginWidget(self):
    try:
      self.loginW.show()
    except AttributeError:
      self.loginW = LoginWidget(self.__mainEngine, self)
      self.loginW.show()

class LoginWidget(QtGui.QDialog):

  def __init__(self, mainEngine, parent=None):
    super(LoginWidget, self).__init__()
    self.__mainEngine = mainEngine          # set mainEngine for Widget
    self.initUi()

  def initUi(self):
    ...
    buttonLogin.clicked.connect(self.login) # connect signal/slot
    self.setLayout(grid)

  def login(self):
    ...
    tdAddress = str(self.editTdAddress.text())
    # call mainEngine method in middle layer
    self.__mainEngine.login(
      userid, mdPassword, tdPassword, brokerid, mdAddress, tdAddress)
    self.close()
{% endhighlight %}

### Middle MainEngine Layer

{% highlight python linenos %}
# @file:  demoEngine.py
# @brief: middle layer of the trading system
#         wrap API and event engine into one main engine

from demoApi import *
from eventEngine import EventEngine

class MainEngine:                 # main engine to schedule all APIs

  def __init__(self):
    self.ee = EventEngine()       # create eventEngine for mainEngine
    self.md = DemoMdApi(self.ee)  # create quotation API instance
    self.td = DemoTdApi(self.ee)  # create trader API instance
    self.ee.start()               # start event engine
    self.ee.register(EVENT_TDLOGIN, self.initGet)
    self.ee.register(EVENT_INSTRUMENT, self.insertInstrument)

  def login(self, userid, mdPassword, tdPassword, brokerid, mdAddress, tdAddress):
    self.md.login(mdAddress, userid, mdPassword, brokerid)
    self.td.login(tdAddress, userid, tdPassword, brokerid)

  def subscribe(self, instrumentid, exchangeid):
    self.md.subscribe(instrumentid, exchangeid)
  ...
{% endhighlight %}

{% highlight python linenos %}
# @file: eventType.py
# @brief: Event Type Constants
EVENT_TIMER = 'eTimer'                      # Timer Event (every second)
EVENT_LOG = 'eLog'                          # Log Event
EVENT_TDLOGIN = 'eTdLogin'                  # Trader Server Login Event
EVENT_MARKETDATA = 'eMarketData'            # Market Data Event
EVENT_MARKETDATA_CONTRACT = 'eMarketData.'
EVENT_TRADE = 'eTrade'
EVENT_TRADE_CONTRACT = 'eTrade.'
EVENT_ORDER = 'eOrder'
EVENT_ORDER_ORDERREF = 'eOrder.'
EVENT_POSITION = 'ePosition'
EVENT_INSTRUMENT = 'eInstrument'
EVENT_INVESTOR = 'eInvestor'
EVENT_ACCOUNT = 'eAccount'
{% endhighlight %}

### Bottom API Layer

When callback function is triggered, certain type event ( `EVENT_LOG`, `EVENT_TDLOGIN`, `EVENT_MARKETDATA` etc.) is sent out with the data in its `dict_` (<span class="label label-default">#20-22, 27-29, 35-37</span>). The data will then get processed in the middle engine layer.

{% highlight python linenos %}
# @file: demoApi.py
# @brief: bottom layer of the trading system
#         simplify Python API wrapper

from vnltsmd import MdApi
from vnltstd import *
from eventEngine import *

class DemoMdApi(MdApi): # Wrapper of MdApi

  # all data is sent to the eventEngine
  # event for specific callback function is put in the eventEngine
  def __init__(self, eventEngine):
    super(DemoMdApi, self).__init__()
    self.__eventEngine = eventEngine  # set eventEngine
    ...
    self.createFtdcMdApi(os.getcwd() + '...')

  def onFrontConnected(self):
    event = Event(type_=EVENT_LOG)
    event.dict_['log'] = u'Quotation Server Connected'
    self.__eventEngine.put(event)
    if self.__userid:
      self.reqUserLogin(req, self.__reqid)

  def onRspUserLogin(self, data, error, n, last):
    event = Event(type_=EVENT_LOG)
    event.dict_['log'] = u'Login Succeed'
    self.__eventEngine.put(event)
    if self.__setSubscribed:
      for instrument in self.__setSubscribed:
        self.subscribe(instrument[0], instrument[1])

  def onRtnDepthMarketData(self, data):
    event1 = Event(type_=EVENT_MARKETDATA)
    event1.dict_['data'] = data
    self.__eventEngine.put(event1)
    event2 = Event(type_=(EVENT_MARKETDATA_CONTRACT+data['InstrumentID']))
    event2.dict_['data'] = data
    self.__eventEngine.put(event2)

  def login(self, address, userid, password, brokerid):
    self.__userid = userid
    self.__password = password
    self.__brokerid = brokerid
    self.registerFront(address)
    self.init()   # Initialize, onFrontConnected() will be invoked

  def subscribe(self, instrumentid, exchangeid):
    req = {}
    req['InstrumentID'] = instrumentid
    req['ExchangeID'] = exchangeid
    self.subscribeMarketData(req)
    instrument = (instrumentid, exchangeid)
    self.__setSubscribed.add(instrument)

class DemoTdApi(TdApi): # Wrapper of TdApi
  ...
{% endhighlight %}


{% include links.html %}
