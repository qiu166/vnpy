---
name: vnpy-quant
description: 基于vnpy量化交易框架，编写CTA/套利/算法交易Python策略代码、回测代码、解释vnpy接口与案例；Use when用户请求vnpy量化策略开发、代码编写、排错、框架讲解；Produces可运行的vnpy策略源码、代码注释、步骤说明。
version: 1.0.0
license: Apache-2.0
compatibility: Python >=3.9, vnpy>=4.0
metadata:
  author: yourname
  category: development
  tags: ["vnpy","量化交易","cta策略","回测","python"]
---

# vnpy-quant 量化技能
## 概述
本技能封装vnpy(VeighNa)量化框架知识，支持编写、解读、调试基于vnpy的量化交易代码。
vnpy是开源Python量化平台，支持A股、期货、期权，内置CTA策略、回测、价差套利、算法交易TWAP/冰山算法、行情录制等模块。

## 使用场景（触发条件）
✅ 适用：
1. 用户要求编写 vnpy CtaTemplate 趋势策略、网格策略、套利策略代码
2. 对vnpy策略代码进行解读、查bug、优化参数
3. 使用vnpy CtaBacktester 做历史回测代码
4. 讲解vnpy Gateway、行情Tick/K线、风控模块使用
❌ 不适用：
1. 实盘交易开户、资金对接、券商API申请（仅提供代码层面）
2. 非vnpy的其他量化框架（backtrader、qlib等），应切换其他技能

## 执行指引
1. 确认用户需求：品种（期货/A股）、策略类型（CTA/价差套利/算法拆单）、指标（MACD/ATR/布林等）
2. 基于vnpy官方模板生成代码，优先使用CtaTemplate模板，添加完整注释
3. 输出代码附带：依赖安装命令、运行步骤、回测配置说明
4. 代码必须兼容 vnpy 4.x 社区版语法，不写过时API
5. 提醒用户：代码仅作学习回测演示，不构成投资建议

## 输出规范
- Python代码块，可直接复制进strategies目录
- 代码头部写明策略逻辑、参数说明
- 策略必须继承vnpy内置模板（CtaTemplate / SpreadStrategyTemplate）
- 最后附带风险提示：**本策略仅用于回测学习，不构成任何投资建议，实盘有巨大风险**

## 示例
> 用户需求：写一个vnpy简单双均线CTA策略
```python
from vnpy.trader.utility import BarGenerator, ArrayManager
from vnpy.trader.strategy import CtaTemplate

class DoubleMaStrategy(CtaTemplate):
    author = "skill"
    fast_window = 10
    slow_window = 20

    def __init__(self, cta_engine, strategy_name, vt_symbol, setting):
        super().__init__(cta_engine, strategy_name, vt_symbol, setting)
        self.bg = BarGenerator(self.on_bar)
        self.am = ArrayManager()

    def on_tick(self, tick):
        self.bg.update_tick(tick)

    def on_bar(self, bar):
        am = self.am
        am.update_bar(bar)
        if not am.inited:
            return
        fast_ma = am.sma(self.fast_window)
        slow_ma = am.sma(self.slow_window)
        if fast_ma > slow_ma and self.pos == 0:
            self.buy(bar.close_price, 1)
        elif fast_ma < slow_ma and self.pos >0:
            self.sell(bar.close_price,1)
        self.put_event()
