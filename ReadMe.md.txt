**데이터 셋 내의 각 집단의 분포가 모두 동일하게 나타나고 있습니다!**<br>
**이 경우 이 데이터 셋은 random Clinical Trial(랜덤 실험)으로 진행되어 각 집단이 Homogeneous(균일) 성질을 만족하는 것을 알수 있습니다**

1. 각 처치(Treatment)와 그에 대한 Target 변수들이 독립임을 알 수 있고,
2. 0<P(Z=1|X)<1 을 만족함을 알 수 있습니다. <br> (이 의미는 Feature가 주어졌을 때 처치가 어느 한쪽으로 치우쳐져 있지 않음을 의미합니다. <br> 즉, 남성(men=1) 모두에게 처치가 가해져 있지 않다. 데이터셋 내 집단에게 균등하게 처치가 이루어 졌다. 이런 식으로 이해하면 될 것 같습니다.)

이 __두가지 조건__ 을 만족하면 우리는 Strong Ignorability Assumption이 만족하였다고 합니다.<br>
이 것을 만족한다면 일반적인 상관관계를 통해서도 인과관계를 추론할 수 있게 됩니다.<br>
또한 단순 집단 간의 평균의 차이를 구해 처치(Treatment)가 어떠한 차이를 만들어내는지 계산할 수 있게 됩니다.
<br>
하지만 만일 위의 조건이 만족하지 않는다면, 처치(Treatment)와 결과(Target)에 모두 영향을 주는 교란변수(Confounder)를 도메인 지식으로 찾아내야 합니다. 가령 아래 데이터 셋으로 예를 들면 History가 높은 사람에게는 광고 메일을 보내지 않았고, Recency가 오래된 고객들에게는 광고 메일을 보낸 분포가 나왔다면 History와 Recency가 교란 변수가 되고, 이 교란변수를 조건부로 하여 교란변수의 효과를 상쇄시킨 뒤 차이를 계산하는 CATE(Conditional Average Treatment Estimation)을 진행해야 하며 이 과정은 꽤나 복잡합니다. 
<br>이는 Heterogeneous한 집단에서 인과관계를 추론하는 방식이며 저희 데이터셋은 Homogeneous하므로 해당은 되지 않습니다. 
<br>
CATE를 추정하는 방식을 간략하게 설명하면 다음과 같습니다. 

1. Counfounders를 찾아낸 뒤 이를 조건부로 하여 처치(Treatment)를 받을 확률을 계산합니다. <br> (P(Z=1|X) where Z : Treatment, X : Confounders)
2. 우리는 이를 Propensity Score(경향성 점수)라고 합니다. 경향성 점수를 사용하는 이유는 교란변수를 조건부로 두었기에 교란변수의 영향을 상쇄시킬 수 있기 때문입니다. 
3. 또한, Propensity Score를 input으로 하고, 우리가 관측한 결과(여기에서는 Target값들 즉, Target visit, spend, Conversion이 됩니다.)를 Target으로 하는 Response Function을 추정합니다. 
4. 이 때 머신러닝 알고리즘이 사용됩니다. 주로 트리 기반으로 분류 알고리즘인 XGBoost, LightGBM, RandomForest 등이 사용되며 적용되는 방법은 3가지가 있습니다 <br>
<ul>
<li>T-Learner : Two-model method라고 불리며, treatment를 받은 집단과 그렇지 않은 집단을 다른 모델로 학습하여 각각의 Treatment Effect를 계산하여 그 차이를 계산합니다.</li>
<li>S-Learner : One -model method라고 불리며, 하나의 모델로 학습을 한 뒤 treatment를 받은 집단과 그렇지 않은 집단으로 나누어 Treatment Effect를 계산하여 그 차이를 계산합니다. </li>
<li>X-Learner : Two-model로 우선 학습을 한 뒤 treatment를 받은 집단과 그렇지 않은 집단을 Cross하여 결과를 예측하여 가상의 예측값을 추론합니다. 그리고 개개인의 Treatment Effect를 계산하여 그 차이를 평균하여 CATE를 계산합니다.</li>
</ul><br>
5. 우리는 이 과정을 통해 동질하지 않은 집단에서 광고가 효과가 있었는지 그 효과를 추정한다고 합니다
6. 즉. 우리 데이터셋은 랜덤실험을 만족하는 Homogeneous한 셋이기 때문에 위 과정을 거치지 않고 평균의 차이를 계산하여 광고의 효과를 알아낼 수 있습니다. <br> 다만 ATE 적용 후, S-Learner과 유사하게 하나의 머신러닝 알고리즘 모델로 학습해서 Treatment를 받은 집단과 그렇지 않은 집단을 나눠 광고 집행(Treatment) 효과를 알아볼 예정입니다. 

<참조 : Metalearners for estimating heterogeneous treatment effects using machine learning (Sören R. Künzel , Jasjeet S. Sekhon , Peter J. Bickel , Bin Yu. 2019)>