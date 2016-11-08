---
title: CTP API by Shanghai Future
tags: [quant_api, quant_trading]
keywords: ctp api
last_updated: Nov 7, 2016
summary: "The API of CTP trade server is based on C++ library and carries out the communication between trade client and CTP trade server."
sidebar: quant_trading_sidebar
permalink: quant_trading_ctp_api.html
folder: quant_trading
---

## About CTP API

**Comprehensive Transaction Platform** (**CTP**), a future trade and broker information management system, contains trade server, risk management server, settlement information management subsystem. The **API** is used to communicate with the CTP trade server. From the API, investor can receive quotation data from SHFE, DCE and CZCE, send trading directive to the three exchanges, receive corresponding response and trade status return.

By using the API, trade client could insert or cancel common order and condition order, contract status fire order, query order or trade record and get the current account and position status.

The **communication protocol** between CTP API and CTP trade server is futures TradingData Exchange protocol (**FTD**), an information exchange protocol based on TCP.

{% include note.html content="Some well-known CTP-like APIs include: **CTP** of SFIT, **FEMAS** of CFFEX, **LTS** of HBSC, **X-Speed** of DFITC, **KSFT** of SUNGARD and **UFT** of HangSeng. Each might be introduced in separate article which can be accessed from sidebar navigation." %}

## File Structure of CTP API

{% highlight Bash linenos %}
# header files with ThostFtdc prefix
├── ThostFtdcMdApi.h            # trading interface
├── ThostFtdcTraderApi.h        # quoatation interface
├── ThostFtdcUserApiDataType.h  # all data types
├── ThostFtdcUserApiStruct.h    # all data structures
# api error code and information
├── error.dtd
├── error.xml
# dynamic link library of quotation interface
├── thostmduserapi.dll
├── thostmduserapi.lib
# dynamic link library of trading interface
├── thosttraderapi.dll
├── thosttraderapi.lib
# api for x64 linux
└── x64_linux
    ├── ...
{% endhighlight %}

## CTP Communication Mode

In FTD protocol, communication mode includes the following three modes:

| Mode | Function |
|-------:|-------|
| **Dialog** | Client submits a request to CTP, and CTP returns corresponding results. |
| **Private** | CTP sends private messages to specific client. Those messages are all private notify message such as order status or trade confirmation. |
| **Broadcast** | CTP publishes common information to all clients registered to CTP. |

## Programming Interface Types

| API Type | Class Name |
|-------:|-------|
| **Trader** API | `CThostFtdcTraderApi` and `CThostFtdcTraderSpi` |
| **Quotation** API | `CThostFtdcMdApi` and `CThostFtdcMdSpi` |

### Dialog Mode Programming Interface

{% highlight C++ linenos %}
/// @file:  CTP Trader API
/// @event: request
int CThostFtdcTraderApi::ReqXXX(
  CThostFtdcXXXField *pReqXXX, int nRequestID ) {};
/// @event: response
void CThostFtdcTraderSpi::OnRspXXX(
  CThostFtdcXXXField *pRspXXX, CThostFtdcRspInfoField *pRspInfo,
  int nRequestID, bool bIsLast ) {};

/// @file:  CTP Quatation API
/// @event: request
int CThostFtdcMDApi::ReqXXX(
  CThostFtdcXXXField *pReqXXX, int nRequestID ) {};
/// @event: response
void CThostFtdcMDSpi::OnRspXXX(
  CThostFtdcXXXField *pRspXXX, CThostFtdcRspInfoField *pRspInfo,
  int nRequestID, bool bIsLast) {};

/// @note: reloaded callback function of object inherited
///        from CThostFtdcXXXSpi will be invoked in response
{% endhighlight %}

### Private Mode Programming Interface

{% highlight C++ linenos %}
/// @file: only in CTP Trader API
/// @note: reloaded callback function of object inherited
///        from CThostFtdcTradeSpi will be invoked.
void CThostFtdcTraderSpi::OnRtnXXX( CThostFtdcXXXField *pXXX )
void CThostFtdcTraderSpi::OnErrRtnXXX(
  CThostFtdcXXXField *pXXX, CThostFtdcRspInfoField *pRspInfo )
{% endhighlight %}

### Boadcast Mode Programming Interface

{% highlight C++ linenos %}
/// @file:  only in CTP Trader API
/// @brief: notify client the status change of instruments
void CThostFtdcTraderSpi::OnRtnInstrumentStatus (
  CThostFtdcInstrumentStatusField *pInstrumentStatus )
/// @brief: broadcast updated market quotation data from exchanges
void CThostFtdcTraderSpi::OnRtnDepthMarketData (
  CThostFtdcDepthMarketDataField *pDepthMarketData )
{% endhighlight %}

These four interfaces implement the **FTD protocol**: client submits requests by invoking functions of the `CThostFtdcXXXApi` and receive CTP response with reloaded **callback functions** of their own object inherited from `CThostFtdcXXXSpi`.

## CTP API Specification

### General Rules

To use **Trader API**, client application should follow the steps listed below:

1.  Create a `CThostFtdcTraderApi` instance using static method
2.  Create an event handle instance inherited from `CThostFtdcTraderSpi` interface
3.  Register this event handle instance with `RegisterSpi{}` method
3.  Subscribe **private stream** with `SubscribePrivateTopic{}` method
4.  Subscribe **public stream** with `SubscribePublicTopic{}` method
5.  Register trade front addresses of the CTP server with `RegisterFront{}` method
6.  Start connection with CTP server using `Init{}` method
7.  callback function `OnFrontConnected{}` of `CThostFtdcTraderSpi` be invoked
8.  Submit login request with `ReqUserLogin{}` method
9.  callback function `OnRspUserLogin{}` of `CThostFtdcTraderSpi` be invoked
10. Use other CTP APIs once communication is established successfully

### `ThostFtdcUserApiDataType.h`

{% highlight C++ linenos %}
/// @file  ThostFtdcUserApiDataType.h
/// @brief all basic data types in BLL

typedef char TThostFtdcProductClassType; // type of product class
#define THOST_FTDC_PC_Futures '1'
#define THOST_FTDC_PC_Options '2'
...
{% endhighlight %}

### `ThostFtdcUserApiStruct.h`

{% highlight C++ linenos %}
/// @file  ThostFtdcUserApiStruct.h
/// @brief data structure in BLL

#include "ThostFtdcUserApiStruct.h"

/// Product
struct CThostFtdcProductField
{
  TThostFtdcInstrumentIDType ProductID;
  TThostFtdcProductNameType  ProductName;
  TThostFtdcExchangeIDType   ExchangeID;
  TThostFtdcProductClassType ProductClass;
  ...
};
...
{% endhighlight %}

### `ThostFtdcMdApi.h`

{% highlight C++ linenos %}
/// @file  ThostFtdcMdApi.h
/// @brief market data interface for client

#include "ThostFtdcUserApiStruct.h"

/// callback interface for market data
class CThostFtdcMdSpi
{
public:
  /// user login callback
  /// @see ReqUserLogin()
  virtual void OnRspUserLogin(
    CThostFtdcRspUserLoginField *pRspUserLogin,
    CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast) {};

  /// market data subscription callback
  /// @see SubscribeMarketData()
  virtual void OnRspSubMarketData(
    CThostFtdcSpecificInstrumentField *pSpecificInstrument,
    CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast) {};
  ...
};

/// function call interface for market data
class MD_API_EXPORT CThostFtdcMdApi
{
public:
  /// static method to create MdApi
  static CThostFtdcMdApi *CreateFtdcMdApi(const char *pszFlowPath = "");

  /// register callback
  /// @param pSpi - instance of callback class MdSpi
  virtual void RegisterSpi(CThostFtdcMdSpi *pSpi) = 0;

  /// market data subscription
  virtual int SubscribeMarketData(
    char *ppInstrumentID[], int nCount, char* pExchageID) = 0;

  /// user login request
  virtual int ReqUserLogin(
    CThostFtdcReqUserLoginField *pReqUserLoginField, int nRequestID) = 0;
  ...
protected:
  ~CThostFtdcMdApi(){};
};
{% endhighlight %}

### `ThostFtdcTraderApi.h`

{% highlight C++ linenos %}
/// @file  ThostFtdcTraderApi.h
/// @brief trading interface for client

#include "ThostFtdcUserApiStruct.h"

/// callback interface for trading
class CThostFtdcTraderSpi
{
public:
  /// user login callback
  /// @see ReqUserLogin()
  virtual void OnRspUserLogin(
    CThostFtdcRspUserLoginField *pRspUserLogin,
    CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast) {};

  /// order action callback
  /// @see ReqOrderAction()
  virtual void OnRspOrderAction(
    CThostFtdcInputOrderActionField *pInputOrderAction,
    CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast) {};
  ...
};

/// function call interface for trading
class TRADER_API_EXPORT CThostFtdcTraderApi
{
public:
  /// static method to create TraderApi
  static CThostFtdcTraderApi *CreateFtdcTraderApi(const char *pszFlowPath = "");

  /// register callback
  /// @param pSpi - instance of callback class TraderSpi
  virtual void RegisterSpi(CThostFtdcTraderSpi *pSpi) = 0;

  /// user login request
  virtual int ReqUserLogin(
    CThostFtdcReqUserLoginField *pReqUserLoginField, int nRequestID) = 0;

  /// order action request
  virtual int ReqOrderAction(
    CThostFtdcInputOrderActionField *pInputOrderAction, int nRequestID) = 0;
  ...
protected:
  ~CThostFtdcTraderApi(){};
};
{% endhighlight %}

## CTP Trader API Test Demo

### `TraderSpi.h`

{% highlight C++ linenos %}
/// @file:  TraderSpi.h
/// @brief: class inherited from CThostFtdcTraderSpi

#include "ThostFtdcTraderApi.h"

class CTraderSpi : public CThostFtdcTraderSpi
{
public:
  virtual void OnFrontConnected( );
  virtual void OnRspUserLogin( CThostFtdcRspUserLoginField *pRspUserLogin,
    CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast );
  ...
  /// @brief: investor posistion query response
  virtual void OnRspQryInvestorPosition(
    CThostFtdcInvestorPositionField *pInvestorPosition,
    CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast );
  ...
  virtual void OnRtnOrder(CThostFtdcOrderField *pOrder);
  virtual void OnRtnTrade(CThostFtdcTradeField *pTrade);

private:
  void ReqUserLogin();
  void ReqQryInvestorPosition();
  ...
};
{% endhighlight %}

### `TraderSpi.cpp`

{% highlight C++ linenos %}
/// @file:  TraderSpi.cpp
/// @brief: method implementations for CTraderSpi class

#include "ThostFtdcTraderApi.h"
#include "TraderSpi.h"

/// @brief: extern TraderAPI instance and config params from main cpp
extern CThostFtdcTraderApi* pUserApi;
extern char FRONT_ADDR[];   // front address
extern char BROKER_ID[];    // broker id
extern char INVESTOR_ID[];  // invester id
extern int  iRequestID;     // request id
...
/// @brief: session params
TThostFtdcFrontIDType   FRONT_ID;
TThostFtdcSessionIDType SESSION_ID;
...

void CTraderSpi::OnFrontConnected()
{
  cerr << "OnFrontConnected" << endl;
  ReqUserLogin();
}

void CTraderSpi::ReqUserLogin()
{
  CThostFtdcReqUserLoginField req;
  memset( &req, 0, sizeof(req) );
  strcpy( req.BrokerID, BROKER_ID );
  strcpy( req.UserID, INVESTOR_ID );
  strcpy( req.Password, PASSWORD );
  int iResult = pUserApi->ReqUserLogin( &req, ++iRequestID );
  cerr << "Login Reqest: " << iResult
       << ((iResult == 0) ? ", Success" : ", Fail") << endl;
}

void CTraderSpi::OnRspUserLogin(
  CThostFtdcRspUserLoginField *pRspUserLogin,
  CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast )
{
  cerr << "OnRspUserLogin" << endl;
  if (bIsLast && !IsErrorRspInfo(pRspInfo)) {
    /// save session params
    FRONT_ID = pRspUserLogin->FrontID;
    SESSION_ID = pRspUserLogin->SessionID;
    ...
    /// confirm settlement info
    ReqSettlementInfoConfirm();
  }
}

void CTraderSpi::ReqQryInvestorPosition()
{
  CThostFtdcQryInvestorPositionField req;
  memset( &req, 0, sizeof(req) );
  strcpy( req.BrokerID, BROKER_ID );
  strcpy( req.InvestorID, INVESTOR_ID );
  strcpy( req.InstrumentID, INSTRUMENT_ID );
  while (true) {
    int iResult = pUserApi->ReqQryInvestorPosition( &req, ++iRequestID );
    if (!IsFlowControl(iResult)) {
      cerr << "Position Query: "  << iResult
           << ((iResult == 0) ? ", Success" : ", Fail") << endl;
      break;
    } else {
      cerr << "Position Query "  << iResult << ", Flow Controled" << endl;
      Sleep(1000);
    }
  } // while
}

void CTraderSpi::OnRspQryInvestorPosition(
  CThostFtdcInvestorPositionField *pInvestorPosition,
  CThostFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast )
{
  cerr << "OnRspQryInvestorPosition" << endl;
  if (bIsLast && !IsErrorRspInfo(pRspInfo)) {
    ReqOrderInsert();
    ReqExecOrderInsert();
    ReqForQuoteInsert();
    ReqQuoteInsert();
  }
}
...
{% endhighlight %}

### `TraderSpi.cpp`

{% highlight C++ linenos %}
/// @file:  testTraderApi.cpp
/// @brief: main entry of the application

#include "ThostFtdcTraderApi.h"
#include "TraderSpi.h"

/// TraderApi instance
CThostFtdcTraderApi* pUserApi;
/// config params
char  FRONT_ADDR[] = "tcp://......:88888";
TThostFtdcBrokerIDType   BROKER_ID   = "1111";
TThostFtdcInvestorIDType INVESTOR_ID = "123456";
TThostFtdcPasswordType   PASSWORD    = "123456";
...
int iRequestID = 0; // request id

void main(void)
{
  pUserApi = CThostFtdcTraderApi::CreateFtdcTraderApi();  // create instance
  CTraderSpi* pUserSpi = new CTraderSpi();
  pUserApi->RegisterSpi((CThostFtdcTraderSpi*)pUserSpi);  // register Spi
  pUserApi->SubscribePublicTopic(THOST_TERT_QUICK);       // public stream
  pUserApi->SubscribePrivateTopic(THOST_TERT_QUICK);      // private stream
  pUserApi->RegisterFront(FRONT_ADDR);                    // connect
  pUserApi->Init();
  ...
}
{% endhighlight %}

{% include links.html %}
