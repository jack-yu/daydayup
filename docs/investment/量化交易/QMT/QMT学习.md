# QMT学习

# 在线学习

[在线文档--快速开始](https://dict.thinktrader.net/innerApi/start_now.html)

这个好，在线的，随时随地可学。

## 学习记录

其实最好去看在线文档，这里只是摘录不完整又没有图。

1. QMT 系统支持回测模型与实盘模型。
1. 回测模型： 指在历史 k 线上，自左向右逐根遍历 k 线，以模拟的资金账号记录每日的买卖信号，持仓盈亏，最终展示策略在历史上的净值走势结果。
    1. 首先需要下载历史行情，首次下载可以在界面左上角，点击操作，选择数据管理补充行情，选择回测的周期，如日线，所需的板块数据，如沪深A股板块，时间范围选择全部，下载完整历史行情。其次设置每日定时更新，可以点击客户端右下角行情按钮，在批量下载界面选择需要每天更新的数据，勾选定时下载选项，之后每天在指定时间会自动下载行情数据到本地。
    1. 回测模型取本地数据遍历，不需要向服务器订阅实时行情，应使用 get_market_data_ex函数，指定subscribe参数为False，来读取本地行情数据。
    1. 回测模型的撮合规则为，指定交易价格在当前k线高低点间的，按指定价格撮合，超过高低点的，按当前 k 线收盘价撮合。委托数量大于可用数量时，按可用数量撮合。
    1. 回测模型右侧的基本信息，如默认周期，默认主图，在我的界面点击回测时会生效。在行情界面k线下点击回测，以当前 k 线的周期，品种为准。回测必须以副图模式执行，不要选择主图 /主图叠加
1. 实盘模型： 指在盘中收取最新的动态行情，即时发送买卖信号到交易所，判断委托状态，需要实时重复报撤的模型。
    1. 实盘模型也分模拟柜台模拟交易和真实柜台实盘交易两种。具体请参考如何配置账号
    1. 你要运行实盘模型，QMT 系统提供两种交易模式：
        - 默认的交易模式为逐 k 线生效 (passorder函数快速交易quicktrade参数填 0 即默认值)，适用与需要在盘中模拟历史上逐 k 线的效果需求。例如选择一分钟周期，将下单判断，下单函数放在handlebar函数内，盘中主图每个分笔 (三秒一次)会触发一次handlebar函数调用，系统会暂存当前handlebar产生的下单信号。三秒后下一个分笔到达时，如果是新的一分钟 k 线的第一个分笔，判断上一个分笔为前一根k线最后分笔，会将暂存的交易信号发送给交易所，完成交易。如到达的下一个分笔不是新一根 k 线的，则判定当前 k 线未完成，丢弃暂存的交易信号。1 分钟 k 线情形，每根k线内会有 20 个分笔，前 19 个分笔产生的信号会被丢弃，最后一个分笔的信号，会在下一根k线，首个分笔到达时，延迟三秒发出。系统自带的ContxtInfo也做了同样的等待，回退处理，逐 k 线模式的交易记录可以保存在ContextInfo对象的属性中。
        - QMT 系统也支持立即下单的交易模式，passorder函数的快速交易quicktrade参数填 2，可以在运行后立刻发出委托，不对信号进行等待，丢弃的操作。此时需要用普通的全局变量(如自定义一个Class a())保存委托状态，不能存在ContextInfo的属性里。
    1. 实盘的撮合规则以交易所为准。股票品种的话，价格不能超过 2% 的价格笼子否则废单。数量超过可用数量时会废单。
    1. 实盘模型需要在模型交易界面执行。模型交易界面，选择新建策略交易，添加需要的模型。运行模式可以选择模拟或实盘。
        - 选择模拟信号模式，在策略信号界面显示买卖信号，不实际发出委托。
        - 选择实盘交易模式，显示的策略信号会实际发出到交易所。
    > 提示<br>
运行模式的模拟和实盘，与您使用的账号实际是实盘账号（真实交易所柜台）或是模拟账号（模拟交易柜台）无关。相关账号申请需要联系您做所在券商的工作人员，或者购买投研端账号获取模拟柜台撮合服务。

# 2025年2月19日20:28:14

*还是得看官方文档啊，大模型都说不明白。。。*

QMT 系统提供两大类(事件驱动与定时任务)，共三种运行机制。

![](https://dict.thinktrader.net/assets/%E5%86%85%E7%BD%AEAPI_%E4%B8%89%E7%A7%8D%E6%9C%BA%E5%88%B6%E5%AF%B9%E6%AF%94-bc32690d.png)


# 2025年2月21日17:49:10

继续学习官方文档。

### 逐 K 线驱动：handlebar
handlebar是主图历史 k 线+盘中订阅推送。运行开始时，所选周期历史 k 线从左向右每根触发一次handlebar函数调用。盘中时，主图品种每个新分笔数据到达，触发一次handlebar函数调用。

*啥叫新分笔数据？？？*

### 事件驱动 ：subscribe 订阅推送
盘中订阅指定品种的分笔数据，新分笔到达时，触发指定的回调函数。

### 定时任务 ：run_time 定时运行
指定固定的时间间隔，持续触发指定的回调函数

看客服给的视频里用的是第一个handlebar方法。

如果要定时处理应该用第三个方法“定时任务”吧。

第二个“事件驱动”没写明白。先放这里吧。

看起来回测只能用handlebar，实盘三种都可以用啊。

![](https://dict.thinktrader.net/assets/%E5%86%85%E7%BD%AEAPI_%E4%B8%8D%E5%90%8C%E6%9C%BA%E5%88%B6%E5%8C%B9%E9%85%8D%E4%B8%8D%E5%90%8C%E5%9C%BA%E6%99%AF%E9%9C%80%E6%B1%82-6942d075.png)


## 逐 K 线驱动（handlebar）示例

这个好，举个例子~~

> 在编写一个策略时，首先需要在代码的最前一行写上： #coding:gbk 统一脚本的编码格式是GBK

为啥不是utf-8 :joy:

直接给代码了

```
#coding:gbk

#导入常用库
import pandas as pd
import numpy as np
import talib
#示例说明：本策略，通过计算快慢双均线，在金叉时买入，死叉时做卖出 点击回测运行 主图选择要交易的股票品种

def init(C):
	#init handlebar函数的入参是ContextInfo对象 可以缩写为C
	#设置测试标的为主图品种
	C.stock= C.stockcode + '.' +C.market
	#line1和line2分别为两条均线期数
	C.line1=10   #快线参数
	C.line2=20   #慢线参数
	#accountid为测试的ID 回测模式资金账号可以填任意字符串
	C.accountid = "testS"  

def handlebar(C):
	#当前k线日期
	bar_date = timetag_to_datetime(C.get_bar_timetag(C.barpos), '%Y%m%d%H%M%S')
	#回测不需要订阅最新行情使用本地数据速度更快 指定subscribe参数为否. 如果回测多个品种 需要先下载对应周期历史数据 
	local_data = C.get_market_data_ex(['close'], [C.stock], end_time = bar_date, period = C.period, count = max(C.line1, C.line2), subscribe = False)
	close_list = list(local_data[C.stock].iloc[:, 0])
	#将获取的历史数据转换为DataFrame格式方便计算
	#如果目前未持仓，同时快线穿过慢线，则买入8成仓位
	if len(close_list) <1:
		print(bar_date, '行情不足 跳过')
	line1_mean = round(np.mean(close_list[-C.line1:]), 2)
	line2_mean = round(np.mean(close_list[-C.line2:]), 2)
	print(f"{bar_date} 短均线{line1_mean} 长均线{line2_mean}")
	account = get_trade_detail_data('test', 'stock', 'account')
	account = account[0]
	available_cash = int(account.m_dAvailable)
	holdings = get_trade_detail_data('test', 'stock', 'position')
	holdings = {i.m_strInstrumentID + '.' + i.m_strExchangeID : i.m_nVolume for i in holdings}
	holding_vol = holdings[C.stock] if C.stock in holdings else 0
	if holding_vol == 0 and line1_mean > line2_mean:
		vol = int(available_cash / close_list[-1] / 100) * 100
		#下单开仓
		passorder(23, 1101, C.accountid, C.stock, 5, -1, vol, C)
		print(f"{bar_date} 开仓")
		C.draw_text(1, 1, '开')
	#如果目前持仓中，同时快线下穿慢线，则全部平仓
	elif holding_vol > 0 and line1_mean < line2_mean:
		#状态变更为未持仓
		C.holding=False
		#下单平仓
		passorder(24, 1101, C.accountid, C.stock, 5, -1, holding_vol, C)
		print(f"{bar_date} 平仓")
		C.draw_text(1, 1, '平')

```

这代码就是刚才视频里讲的例子啊。

> #回测不需要订阅最新行情使用本地数据速度更快 指定subscribe参数为否. 如果回测多个品种 需要先下载对应周期历史数据 
	
这段代码意思不难理解。细节还有地方不清楚，特别是passorder方法的调用，好多硬编码。。。写文档的人和做框架的人python水平估计很一般啊 。。。 :joy:

## 实盘示例-基于 handlebar

上面是回测的例子，下面讲实盘。很合理。

>Handlebar 方法会在历史 K 线上逐 K 线调用，系统会保存函数所做更改。<br>
在盘中交易时间，handlebar 函数会随行情推送（tick 数据）被调用，当一个 tick 数据为所在 K 线最后一个 tick 时，此 tick 调用的 handlebar 所做的更改会被系统保存，如有交易指令，会在下一根K 线的第一个 tick 到来时发送；其他 tick 可以打印运行结果，但 handlebar 所做更改不会被保存，也不会发送交易信号。

这一段写的不是很清楚呢，盘中K线是什么意思，1分钟K线？5分钟、小时K线？tick是几秒来一次？

先看看代码吧：

```
#coding:gbk

# 导入包
import pandas as pd
import numpy as np
import datetime

"""
示例说明：双均线实盘策略，通过计算快慢双均线，在金叉时买入，死叉时做卖出
"""

class a():
	pass
A = a() #创建空的类的实例 用来保存委托状态 


def init(C):
	A.stock= C.stockcode + '.' + C.market #品种为模型交易界面选择品种
	A.acct= account #账号为模型交易界面选择账号
	A.acct_type= accountType #账号类型为模型交易界面选择账号
	A.amount = 10000 #单笔买入金额 触发买入信号后买入指定金额
	A.line1=17   #快线周期
	A.line2=27   #慢线周期
	A.waiting_list = [] #未查到委托列表 存在未查到委托情况暂停后续报单 防止超单
	A.buy_code = 23 if A.acct_type == 'STOCK' else 33 #买卖代码 区分股票 与 两融账号
	A.sell_code = 24 if A.acct_type == 'STOCK' else 34
	print(f'双均线实盘示例{A.stock} {A.acct} {A.acct_type} 单笔买入金额{A.amount}')

def handlebar(C):
	#跳过历史k线
	if not C.is_last_bar():
		return
	now = datetime.datetime.now()
	now_time = now.strftime('%H%M%S')
	# 跳过非交易时间
	if now_time < '093000' or now_time > "150000":
		return
	account = get_trade_detail_data(A.acct, A.acct_type, 'account')
	if len(account)==0:
		print(f'账号{A.acct} 未登录 请检查')
		return
	account = account[0]
	available_cash = int(account.m_dAvailable)
	#如果有未查到委托 查询委托
	if A.waiting_list:
		found_list = []
		orders = get_trade_detail_data(A.acct, A.acct_type, 'order')
		for order in orders:
			if order.m_strRemark in A.waiting_list:
				found_list.append(order.m_strRemark)
		A.waiting_list = [i for i in A.waiting_list if i not in found_list]
	if A.waiting_list:
		print(f"当前有未查到委托 {A.waiting_list} 暂停后续报单")
		return
	holdings = get_trade_detail_data(A.acct, A.acct_type, 'position')
	holdings = {i.m_strInstrumentID + '.' + i.m_strExchangeID : i.m_nCanUseVolume for i in holdings}
	#获取行情数据
	data = C.get_market_data_ex(["close"],[A.stock],period = '1d',count = max(A.line1, A.line2)+1)
	close_list = data[A.stock].values
	if len(close_list) < max(A.line1, A.line2)+1:
		print('行情长度不足(新上市或最近有停牌) 跳过运行')
		return
	pre_line1 = np.mean(close_list[-A.line1-1: -1])
	pre_line2 = np.mean(close_list[-A.line2-1: -1])
	current_line1 = np.mean(close_list[-A.line1:])
	current_line2 = np.mean(close_list[-A.line2:])
	#如果快线穿过慢线，则买入委托 当前无持仓 买入
	vol = int(A.amount / close_list[-1] / 100) * 100 #买入数量 向下取整到100的整数倍
	if A.amount < available_cash and vol >= 100 and A.stock not in holdings and pre_line1 < pre_line2 and current_line1 > current_line2:
		#下单开仓 ，参数说明可搜索PY交易函数 passorder
		msg = f"双均线实盘 {A.stock} 上穿均线 买入 {vol}股"
		passorder(A.buy_code, 1101, A.acct, A.stock, 14, -1, vol, '双均线实盘', 2 , msg, C)
		print(msg)
		A.waiting_list.append(msg)
	#如果快线下穿慢线，则卖出委托
	if A.stock in holdings and holdings[A.stock] > 0 and pre_line1 > pre_line2 and current_line1 < current_line2:
		msg = f"双均线实盘 {A.stock} 下穿均线 卖出 {holdings[A.stock]}股"
		passorder(A.sell_code, 1101, A.acct, A.stock, 14, -1, holdings[A.stock], '双均线实盘', 2 , msg, C)
		print(msg)
		A.waiting_list.append(msg)

```


这段代码有点东西，至少把几个硬编码的数字是干啥的讲了。虽然大概也猜到了。

>A.buy_code = 23 if A.acct_type == 'STOCK' else 33 #买卖代码 区分股票 与 两融账号<br>
A.sell_code = 24 if A.acct_type == 'STOCK' else 34




# 文档学习

### 国金QMT说明文档
 
[国金QMT极速策略交易系统_模型资料_Python_API_说明文档_Python3.pdf](../../../../resources/量化/国金QMT极速策略交易系统_模型资料_Python_API_说明文档_Python3.pdf)

[国金QMT极速策略交易系统-普通算法交易参数说明.pdf](../../../../resources/量化/国金QMT极速策略交易系统-普通算法交易参数说明.pdf)

[国金QMT极速策略交易系统-网格策略使用手册202002.pdf](../../../../resources/量化/国金QMT极速策略交易系统-网格策略使用手册202002.pdf)

[国金QMT极速策略交易系统-智能算法交易介绍202008.pdf](../../../../resources/量化/国金QMT极速策略交易系统-智能算法交易介绍202008.pdf)


[国金QMT极速策略交易系统-VBA模型编辑使用手册202002.pdf](../../../../resources/量化/国金QMT极速策略交易系统-VBA模型编辑使用手册202002.pdf)



还有一个交易系统使用说明文档，里面太多图形化操作说明图片，文件太大了55M，内容上没什么就先不放了。VBA可以先不看，重点看看python。



# 案例学习


- 国金QMT终端自带的一个策略

### 多因子选股回测示例

不太行，终端自带的策略不是完整的python代码，没有讲整体框架，看不太明白。似乎应该是需要重写几个方法然后被框架调用。方法被调用的时机和次数，几个参数的具体数据结构都没有讲。还得再去找其他资料了。

```
#coding:gbk
"""
回测模型示例（非实盘交易策略）
#HS300日线下运行，20个交易日进行 一次调仓，每次买入在买入备选中因子评分前10的股票，每支股票各分配当前可用资金的10%（权重可调整）
#扩展数据需要在补完HS300成分股数据之后生成，本模型中扩展数据暂时使用VBA指标ATR和ADTM生成，命名为atr和adtm
"""
import pandas as pd
import numpy as np
import time
import datetime

def init(ContextInfo):
	ContextInfo.s = ContextInfo.get_sector('000300.SH')
	ContextInfo.set_universe(ContextInfo.s)
	ContextInfo.day = 0
	ContextInfo.holdings = {i:0 for i in ContextInfo.s}
	ContextInfo.weight = [0.1]*10         #设置资金分配权重
	ContextInfo.buypoint = {}
	ContextInfo.money = ContextInfo.capital
	ContextInfo.profit = 0
	ContextInfo.accountID='testS'

def handlebar(ContextInfo):
	rank1 = {}
	rank2 = {}
	rank_total = {}
	tmp_stock = {}
	d = ContextInfo.barpos
	price = ContextInfo.get_history_data(1,'1d','open',3)
	if d > 60 and d % 20 == 0:               #每月一调仓
		nowDate = timetag_to_datetime(ContextInfo.get_bar_timetag(d),'%Y%m%d')
		print(nowDate)
		buys, sells = signal(ContextInfo)
		order = {}
		for k in list(buys.keys()):
			if buys[k] == 1:
				rank1[k] = ext_data_rank('atr',k[-2:]+k[0:6],0,ContextInfo)
				rank2[k] = ext_data_rank('adtm',k[-2:]+k[0:6],0,ContextInfo)
				#print rank1[k], rank2[k]
				rank_total[k] = 1.0 * rank1[k]                          #因子的权重需要人为设置，此处取了0.5和-0.5
				print (1111111, rank1[k])
		tmp = sorted(list(rank_total.items()), key = lambda item:item[1])
		#print tmp
		if len(tmp) >= 10:
			tmp_stock = {i[0] for i in tmp[:10]}
		else:
			tmp_stock = {i[0] for i in tmp}                              #买入备选中若超过10只股票则选10支，不足10支则全选
		for k in list(buys.keys()):
			if k not in tmp_stock:
				buys[k] = 0
		if tmp_stock:
			print('stock pool:',tmp_stock)
			for k in ContextInfo.s:
				if ContextInfo.holdings[k] > 0 and sells[k] == 1:
					print('ready to sell')
					order_shares(k,-ContextInfo.holdings[k]*100,'fix',price[k][-1],ContextInfo,ContextInfo.accountID)
					ContextInfo.money += price[k][-1] * ContextInfo.holdings[k] * 100 - 0.0003*ContextInfo.holdings[k]*100*price[k][-1]                  #手续费按万三设定
					ContextInfo.profit += (price[k][-1]-ContextInfo.buypoint[k]) * ContextInfo.holdings[k] * 100 - 0.0003*ContextInfo.holdings[k]*100*price[k][-1]
					#print price[k][-1]
					print(k)
					#print ContextInfo.money
					ContextInfo.holdings[k] = 0
			ContextInfo.money_distribution = {k:i*ContextInfo.money for (k,i) in zip(tmp_stock,ContextInfo.weight)}
			for k in tmp_stock:
				if ContextInfo.holdings[k] == 0 and buys[k] == 1:
					print('ready to buy')
					order[k] = int(ContextInfo.money_distribution[k]/(price[k][-1]))/100
					order_shares(k,order[k]*100,'fix',price[k][-1],ContextInfo,ContextInfo.accountID)
					ContextInfo.buypoint[k] = price[k][-1]
					ContextInfo.money -= price[k][-1] * order[k] * 100 - 0.0003*order[k]*100*price[k][-1]
					ContextInfo.profit -= 0.0003*order[k]*100*price[k][-1]
					print(k)
					ContextInfo.holdings[k] = order[k]
			print(ContextInfo.money,ContextInfo.profit,ContextInfo.capital)
	profit = ContextInfo.profit/ContextInfo.capital
	if not ContextInfo.do_back_test:
		ContextInfo.paint('profit_ratio', profit, -1, 0)
		

def signal(ContextInfo):
	buy = {i:0 for i in ContextInfo.s}
	sell = {i:0 for i in ContextInfo.s}
	data_high = ContextInfo.get_history_data(22,'1d','high',3)
	data_high_pre = ContextInfo.get_history_data(2,'1d','high',3)
	data_close60 = ContextInfo.get_history_data(62,'1d','close',3)
	#print data_high
	#print data_close
	#print data_close60
	for k in ContextInfo.s:
		if k in data_close60:
			if len(data_high_pre[k]) == 2 and len(data_high[k]) == 22 and len(data_close60[k]) == 62:
				if data_high_pre[k][-2] > max(data_high[k][:-2]):
					buy[k] = 1           #超过20日最高价，加入买入备选
				elif data_high_pre[k][-2] < np.mean(data_close60[k][:-2]):
					sell[k] = 1           #低于60日均线，加入卖出备选
	#print buy
	#print sell
	return buy,sell           #买入卖出备选

```

