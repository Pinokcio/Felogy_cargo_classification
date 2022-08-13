# Felogy_cargo_classification
[관세청-숭실대 AI융합] 수입화물 우범 여부 예측 인공지능 개발

1. eda
    1. 전체 데이터를 살펴봄 (describe)
    2. 결측치가 있는지 확인해봄 (isna)
        1. 결측치는 임시로 None 값으로 채움
        2. 결측 비율이 너무 큰 column은 학습 데이터에서 제외
    3. 각 feature마다 우범여부가 1로 나타나는 빈도(평균)을 구함
        1. bar, pie, kde plot을 통해 분포를 시각화함
        2. 빈도가 전체 데이터에 대한 우범여부의 비율과 비슷한 데이터는 큰 특징이 없다 판단하고 학습에서 제외
        3. 분포를 시각화하였을 때 어떠한 추세(uniform / gaussian distribution와 같은)를 보이는 데이터는 남김
        4. 특히나 우범여부 빈도가 크게 나타나는 변수가 있는 column은 남김
2. feature engineering
    1. eda 이후 남은 column들은 각 one-hot encoding 혹은 어떠한 전처리 과정을 거침 
    2. 신고번호와 같은 id 값은 버림
    3. numeric 데이터는 그대로 사용
    4. categorical data 중 적출국가코드, 원산지국가코드는 one hot encoding
    5. 신고인부호, 수입자, 해외거래처부호는 우범여부 빈도에 따라 평균보다 많이 큰 값은 0.4 나머지는 0.2 부여
    6. HS10부호코드는 1~2, 3~4, 5~6, 7~10으로 잘라 각각 류, 호, 소호, 세부품목으로 나누어 다름
        1. 이 또한 one hot으로 각각의 정보를 살리면 더욱 정확하겠지만 decision tree에서 one hot encoding은 그렇게 좋은 방법이 아니므로 이들은 부호 그대로 사용함

[Felogy_cargo_classification/felony_cargo_eda.ipynb at main · Pinokcio/Felogy_cargo_classification](https://github.com/Pinokcio/Felogy_cargo_classification/blob/main/felony_cargo_eda.ipynb)

3. modeling

1. 전체적인 특징을 살펴보았을 때 고려해야할 사안이 꽤 있었고, categorical data가 얽혀있는 모습이었기에 decision tree model이 적합하다고 판단 (dt, rf, et)
2. 데이터를 shuffle하고 8:2비율로 train/valid set을 나눔
3. robust하고 정확성 높은 모델을 위해 여러 모델을 ensemble함
    1. voting과 stacking을 고려하였고, 대회 특성상 실제 데이터가 아닌 생성한 데이터이기 때문에 이상치가 적을 것이라 판단하고 stacking을 사용함.
4. 핵심적발 또한 우범여부와 같은 방법을 통해 모델링함

[Felogy_cargo_classification/felony_cargo_최종제출본.ipynb at main · Pinokcio/Felogy_cargo_classification](https://github.com/Pinokcio/Felogy_cargo_classification/blob/main/felony_cargo_%EC%B5%9C%EC%A2%85%EC%A0%9C%EC%B6%9C%EB%B3%B8.ipynb)

후기

1. pandas나 pycaret과 같은 라이브러리에 익숙치 않아, 데이터 분석과 학습을 준비하는데 생각보다 시간이 오래 걸렸습니다.
2. 컴퓨팅 파워가 한정적이기도 했고, 개인적인 실수로 대회를 늦게 시작한 탓에 다른 boosting model이나 regression model 등 여러 모델을 시도하지 못해 아쉬웠습니다.
3. 좀 더 다양한 앙상블 기법과 튜닝을 적용해봤으면 좋았을 것 같습니다.
    1. stacking을 정확히 이해하지 못한채 사용하는데 급급해 제대로 사용하지 못했습니다.
4. 불균형 데이터를 버리거나 그대로 두지 않고, SMOTE나 Downsampling 등을 적용하여 성능향상을 시도해보는 것도 좋았을 것 같습니다.
