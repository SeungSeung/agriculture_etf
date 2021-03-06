# LightGbm 모델을 이용하여 농산물 ETF 가격 등락 예측

## 1. 사용한 주요 feature

a) CBOT 및 CME 곡물선물: 기초 자산의 가격흐름을 추적하기 위해 사용.

b) 미국 농산물선물 ETF: 농업 활동에 전반적인 영향을 미치는 날씨, 자연재해, 비료가격 등락 등 다양한 외부정보를 반영하기 위해 사용.

c) GDP growth: 선물 가격의 특성상 경기의 호불황에 대한 예측과 실제 발표간의 괴리가 가격에 즉각적으로 반영되는 것을 고려하기 위해 사용

d) S&P500,S&P500 선물: 주식시장 자체의 강세/약세를 반영하기 위해 사용 등



## 2. 데이터 전처리

a) 결측치가 전반적으로 많지 않음 

b) 거래량의 결측치는 해당일의 거래량이 매우 적거나 거래가 중단 된 것으로 간주->0으로 입력

c) 종가 결측치는 bias를 최소화-> 이전의 영업일로 처리

d) 상품선물의 특성상 extreme outlier가 다수 등장 but 데이터가 짧기 때문에 단순 drop으로는 손실이 너무 큼 -> clip() method 사용

<pre>
<code>
mod1['tiger_oil_volume']=mod1['tiger_oil_volume'].clip(upper=up_o_vol.max())
</code>
</pre>

e) TIGER 자료에 비해 KDOEX 자료는 더 길이가 짧기 때문에 standard 정규화를 사용하면 outlier에 더 큰 영향을 받을 것으로 판단-> (TIGER->Standard, KODEX-> robust)

f) feature importance 그래프를 참고하여 하위 5개 컬럼을 drop 하였다.

g) grid search 대신 Bayesian Optimization 사용



## 3. 결과를 이용한 단순 백테스팅

a) LightGBM과 데이터의 Test set을 이용하여 익영업일의 각 ETF의 가격 등락 여부를 예측

b) 가격 등락 여부를 예측한 결과 가격 상승이 예측된 경우 매수, 가격 하락이나  예측되거나 전일과 동일할경우 경우 매도 (최대 포지션: 1주, 공매도 x 가정)

c) 슬리피지, 수수료 등을 고려하여 거래비용이 매 거래 시 0.215%가 발생한다고 단순화

d)
tiger etf 수익률: return      1.671533

tiger_strategy    2.814908

kodex etf 수익률: return      1.206234

kodex_strategy    1.259281



