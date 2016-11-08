---
title: LTS API by HwaBao Securities
tags: [quant_api, quant_trading]
keywords: lts api, ctp api
last_updated: Oct 28, 2016
summary: "The LTS API, developed by HwaBao Securietis, is a CTP-like API."
sidebar: quant_trading_sidebar
permalink: quant_trading_lts_api.html
folder: quant_trading
---

## About LTS API

LTS API is a C++ based library. It follows the traditional **CTP** (Comprehensive Transaction Platform) API design pattern. Through this API, investors can receive quotation data, send trading requests, and receive corresponding response and trade status. Like CTP API, LTS API uses **FTD** protocal, which is build on TCP protocal to communicate with the trade server.

{% include note.html content="Some well-known CTP-like APIs include: **CTP** of SFIT, **FEMAS** of CFFEX, **LTS** of HBSC, **X-Speed** of DFITC, **KSFT** of SUNGARD and **UFT** of HangSeng. Each might be introduced in separate article which can be accessed from sidebar navigation." %}

## File Structure of LTS API

{% highlight Bash linenos %}
# header files with SecurityFtdc prefix
├── SecurityFtdcMdApi.h            # quotation interface
├── SecurityFtdcQueryApi.h         # query interface
├── SecurityFtdcTraderApi.h        # trading interface
├── SecurityFtdcUserApiDataType.h  # all data types
├── SecurityFtdcUserApiStruct.h    # all data structures
# dynamic link library of quotation interface
├── securitymduserapi.dll
├── securitymduserapi.lib
├── securitymduserapi.so
# dynamic link library of query interface
├── securityqueryapi.dll
├── securityqueryapi.lib
├── securityqueryapi.so
# dynamic link library of trading interface
├── securitytraderapi.dll
├── securitytraderapi.lib
└── securitytraderapi.so
{% endhighlight %}

## LTS API Implementations

### `SecurityFtdcUserApiDataType.h`

{% highlight C++ linenos %}
/// @file  SecurityFtdcUserApiDataType.h
/// @brief basic data types in BLL

typedef char TSecurityFtdcProductClassType; // type of product class
#define SECURITY_FTDC_PC_Futures '1'
#define SECURITY_FTDC_PC_Options '2'
...
{% endhighlight %}

### `SecurityFtdcUserApiStruct.h`

{% highlight C++ linenos %}
/// @file  SecurityFtdcUserApiStruct.h
/// @brief data structure in BLL

#include "SecurityFtdcUserApiDataType.h"

/// Product
struct CSecurityFtdcProductField
{
  TSecurityFtdcInstrumentIDType ProductID;
  TSecurityFtdcProductNameType  ProductName;
  TSecurityFtdcExchangeIDType   ExchangeID;
  TSecurityFtdcProductClassType ProductClass;
  ...
};
...
{% endhighlight %}

### `SecurityFtdcMdApi.h`

{% highlight C++ linenos %}
/// @file  SecurityFtdcMdApi.h
/// @brief market data interface for client

#include "SecurityFtdcUserApiStruct.h"

/// callback interface for market data
class CSecurityFtdcMdSpi
{
public:
  /// user login callback
  /// @see ReqUserLogin()
  virtual void OnRspUserLogin(
    CSecurityFtdcRspUserLoginField *pRspUserLogin,
    CSecurityFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast) {};

  /// market data subscription callback
  /// @see SubscribeMarketData()
  virtual void OnRspSubMarketData(
    CSecurityFtdcSpecificInstrumentField *pSpecificInstrument,
    CSecurityFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast) {};
  ...
};

/// function call interface for market data
class MD_API_EXPORT CSecurityFtdcMdApi
{
public:
  /// static method to create MdApi
  static CSecurityFtdcMdApi *CreateFtdcMdApi(const char *pszFlowPath = "");

  /// register callback
  /// @param pSpi - instance of callback class MdSpi
  virtual void RegisterSpi(CSecurityFtdcMdSpi *pSpi) = 0;

  /// market data subscription
  virtual int SubscribeMarketData(
    char *ppInstrumentID[], int nCount, char* pExchageID) = 0;

  /// user login request
  virtual int ReqUserLogin(
    CSecurityFtdcReqUserLoginField *pReqUserLoginField, int nRequestID) = 0;
  ...
protected:
  ~CSecurityFtdcMdApi(){};
};
{% endhighlight %}

### `SecurityFtdcTraderApi.h`

{% highlight C++ linenos %}
/// @file  SecurityFtdcTraderApi.h
/// @brief trading interface for client

#include "SecurityFtdcUserApiStruct.h"

/// callback interface for trading
class CSecurityFtdcTraderSpi
{
public:
  /// user login callback
  /// @see ReqUserLogin()
  virtual void OnRspUserLogin(
    CSecurityFtdcRspUserLoginField *pRspUserLogin,
    CSecurityFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast) {};

  /// order action callback
  /// @see ReqOrderAction()
  virtual void OnRspOrderAction(
    CSecurityFtdcInputOrderActionField *pInputOrderAction,
    CSecurityFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast) {};
  ...
};

/// function call interface for trading
class TRADER_API_EXPORT CSecurityFtdcTraderApi
{
public:
  /// static method to create TraderApi
  static CSecurityFtdcTraderApi *CreateFtdcTraderApi(const char *pszFlowPath = "");

  /// register callback
  /// @param pSpi - instance of callback class TraderSpi
  virtual void RegisterSpi(CSecurityFtdcTraderSpi *pSpi) = 0;

  /// user login request
  virtual int ReqUserLogin(
    CSecurityFtdcReqUserLoginField *pReqUserLoginField, int nRequestID) = 0;

  /// order action request
  virtual int ReqOrderAction(
    CSecurityFtdcInputOrderActionField *pInputOrderAction, int nRequestID) = 0;
  ...
protected:
  ~CSecurityFtdcTraderApi(){};
};
{% endhighlight %}

### `SecurityFtdcQueryApi.h`

{% highlight C++ linenos %}
/// @file  SecurityFtdcQueryApi.h
/// @brief query interface for client

#include "SecurityFtdcUserApiStruct.h"

/// callback interface for query
class CSecurityFtdcQuerySpi
{
public:
  /// instrument query callback
  /// @see ReqQryInstrument()
  virtual void OnRspQryInstrument(
    CSecurityFtdcInstrumentField *pInstrument,
    CSecurityFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast) {};

  /// investor position query callback
  /// @see ReqQryInvestorPosition
  virtual void OnRspQryInvestorPosition(
    CSecurityFtdcInvestorPositionField *pInvestorPosition,
    CSecurityFtdcRspInfoField *pRspInfo, int nRequestID, bool bIsLast) {};
  ...
};

/// function call interface for query
class QUERY_API_EXPORT CSecurityFtdcQueryApi
{
public:
  /// static method to create QueryApi
  static CSecurityFtdcQueryApi *CreateFtdcQueryApi(const char *pszFlowPath = "");

  /// register callback
  /// @param pSpi - instance of callback class QueryApi
  virtual void RegisterSpi(CSecurityFtdcQuerySpi *pSpi) = 0;

  /// instrument query request
  virtual int ReqQryInstrument(
    CSecurityFtdcQryInstrumentField *pQryInstrument, int nRequestID) = 0;

  /// investor position query request
  virtual int ReqQryInvestorPosition(
    CSecurityFtdcQryInvestorPositionField *pQryInvestorPosition, int nRequestID) = 0;
  ...
protected:
  ~CSecurityFtdcQueryApi(){};
};
{% endhighlight %}

{% include links.html %}
