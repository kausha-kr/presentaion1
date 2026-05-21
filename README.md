## AI 서비스

GlobalGates의 AI 서비스는 사용자가 입력한 정보나 서비스 데이터를 분석해서, 사람이 더 빠르게 판단할 수 있도록 도와주는 기능입니다.

AI가 모든 결정을 대신하는 것이 아니라, 견적 승인 가능성, 다음 달 예상 매출, 어울리는 전문가 추천처럼 판단에 참고할 수 있는 결과를 제공합니다.

---


### 0. 기획 배경

# KOTRA 해외진출기업 데이터 분석

## 1. 프로젝트 기획 배경

### 분석 목적

국내 기업의 해외 진출 현황을 데이터 기반으로 분석하여, 글로벌 시장 진출 패턴과 산업별 특성을 파악하고자 한다. 또한 국가별·업종별 진출 현황을 시각화하여 향후 해외 시장 진출 전략 수립과 글로벌 판로 개척 플랫폼 구축의 필요성을 설명하는 것을 목표로 한다.

본 분석에서는 공공데이터포털의 KOTRA 해외진출기업 데이터를 활용하여 다음과 같은 내용을 확인한다.

* 국가별 해외 진출 기업 분포
* 업종별 해외 진출 특성
* 지역별 산업 집중 현황
* 투자 형태 및 진출 유형 분석
* 데이터 품질 및 문자열 이상치 탐색

---

## 2. 데이터 수집

### 데이터 출처

* 공공데이터포털
* 대한무역투자진흥공사(KOTRA) 해외진출기업 데이터

### 데이터 개요

해외에 진출한 국내 기업의 정보를 포함하고 있으며 주요 컬럼은 다음과 같다.

| 컬럼명   | 설명           |
| ----- | ------------ |
| 지역    | 해외 진출 지역     |
| 진출국가  | 기업 진출 국가     |
| 관할무역관 | 담당 무역관       |
| 기업명국문 | 기업 한글명       |
| 기업명영문 | 기업 영문명       |
| 주소    | 현지 주소        |
| 우편번호  | 현지 우편번호      |
| 진출형태  | 판매법인, 생산법인 등 |
| 투자형태  | 단독투자, 합작투자 등 |
| 업종대분류 | 산업 대분류       |
| 업종중분류 | 산업 중분류       |

<img width="1824" height="1026" alt="image" src="https://github.com/user-attachments/assets/ae9c8b99-8891-4f1d-8a8d-70117420bffa" />
<img width="1960" height="1258" alt="스크린샷 2026-05-21 092915" src="https://github.com/user-attachments/assets/bfb1f11b-34de-4ad3-bee9-f2622afd7da8" />


## 최종 인사이트
- 해외 진출 기업이 특정 국가와 업종에 집중되어 있어, 신규 해외 진출을 준비하는
- 국내 기업은 시장 정보와 파트너 탐색에 어려움을 겪을 수 있다. 따라서 기업 간 정보를 피드 기반으로 공유하고,
- 해외 바이어·파트너와 연결하는 비즈니스 소셜 마켓 플랫폼이 필요하다.


---
### 1. 전문가 견적 승인 및 거절 예측

사용자가 견적 요청서를 작성하면, AI가 요청 내용을 보고 전문가가 이 요청을 승인할 가능성이 높은지 예측합니다.

예를 들어 요청 내용이 너무 부족하거나 조건이 맞지 않으면 거절될 가능성이 높다고 판단할 수 있고, 내용이 충분하고 전문가와 잘 맞으면 승인될 가능성이 높다고 판단할 수 있습니다.

사용한 모델은 `NB(Naive Bayes)`입니다.

#### 주요 기능

- 견적 요청 내용 분석
- 승인 가능성 예측
- 거절 가능성 예측
- 견적 요청 작성 단계에서 참고 결과 제공

#### 대표 코드

```python
@app.post("/estimation/estimation-regist", response_model=ClassificationResponse)
def predict_estimation_approval(request: EstimationRequest):
```

```python
prediction = estimation_model.predict([request_text])[0]
```

```python
return {"prediction": prediction, "confidence": confidence}
```

---

### 2. 구독자 정보 기반 다음 달 구독 매출 예측

관리자가 현재 구독자 수, 신규 가입자 수, 취소된 구독 수, 마케팅 비용 같은 정보를 입력하면 AI가 다음 달 구독 매출을 예측합니다.

즉, 지금 서비스 상황을 숫자로 넣으면 “다음 달에는 어느 정도 매출이 나올 것 같다”는 값을 알려주는 기능입니다.

사용한 모델은 `RandomForest`입니다.

#### 주요 기능

- 무료 회원, 유료 회원 수 분석
- 최근 신규 구독자 수 분석
- 구독 취소 및 만료 데이터 반영
- 다음 달 예상 구독 매출 계산



#### 대표 코드

```python
@app.post("/regression/predict", response_model=RegressionResponse)
def predict_revenue(request: RegressionRequest):
```

```python
prediction = regression_model.predict(input_df)[0]
```

```python
return {"expectedMonthlyRevenue": round(float(prediction), 2)}
```

---

### 3. 개인 수출입 정보 기반 전문가 매칭

사용자의 관심 분야, 지역, 수출입 정보 등을 바탕으로 가장 잘 맞는 전문가를 추천하는 기능입니다.

예를 들어 사용자가 물류, 통관, 수출 같은 키워드에 관심이 있으면, 비슷한 분야를 가진 전문가를 찾아서 추천합니다.

사용한 방식은 `TF-IDF`와 `벡터 유사도`입니다.

쉽게 말하면, 사용자 정보와 전문가 정보를 숫자로 바꾼 뒤 서로 얼마나 비슷한지 비교해서 가장 잘 맞는 전문가를 찾는 방식입니다.

#### 주요 기능

- 사용자 관심 분야 분석
- 전문가 소개, 지역, 전문 분야 분석
- 사용자와 전문가의 유사도 계산
- 가장 잘 맞는 전문가 목록 추천





#### 대표 코드

```python
@app.post("/recommendation/experts")
def recommend_experts(request: ExpertRecommendationRequest):
```

```python
user_vector = tfidf_vectorizer.transform([user_text])
```

```python
similarities = cosine_similarity(user_vector, expert_vectors)
```

```python
return {"experts": recommended_experts}
```

---



### 4. 채팅 추천 문구 생성

사용자가 채팅을 할 때 바로 보낼 수 있는 짧은 추천 문장을 만들어주는 기능입니다.  
최근 대화 내용, 대화 단계, 사용자 의도, 원하는 말투, 감정 상태를 보고 자연스러운 문장 3개를 추천합니다.

- 사용 기술: LLM, PromptTemplate
- 사용 데이터: 최근 대화, 대화 단계, 사용자 의도, 말투, 감정 상태, 대화 주제, 작성 중인 문장
- 결과: 클릭해서 바로 입력할 수 있는 추천 문구 3개

대표 코드:

```python
prompt_template = PromptTemplate.from_template(template)
prompt = prompt_template.format(**sample.to_dict())

template = '''
다음 채팅 상황을 보고 사용자가 바로 보낼 수 있는 짧은 추천문장 3개를 만들어줘.

최근 대화:
{recent_messages}

대화 단계: {conversation_stage}
사용자 의도: {user_intent}
원하는 말투: {tone}
감정 상태: {emotion_state}
대화 주제: {topic}
작성 중인 문장: {draft_input}
'''

## 사용한 AI 기술

| 기능 | 사용 기술 | 쉽게 설명 |
| --- | --- | --- |
| 전문가 견적 승인 및 거절 예측 | NB | 문장을 보고 승인될지 거절될지 분류하는 기술 |
| 다음 달 구독 매출 예측 | RandomForest | 여러 조건을 보고 숫자 결과를 예측하는 기술 |
| 전문가 매칭 | TF-IDF, 벡터 유사도 | 글자를 숫자로 바꿔서 서로 얼마나 비슷한지 비교하는 기술 |
| 추천문구 | LLM, PromptTemplate | 최근 대화 내용, 대화 단계, 사용자 의도, 원하는 말투, 감정 상태를 보고 자연스러운 문장을 추천해주는 기술 |


---

## AI 서비스 흐름

```text
사용자 또는 관리자 입력
-> AI 서버로 데이터 전달
-> AI 모델이 데이터 분석
-> 예측 결과 또는 추천 결과 반환
-> 웹 화면에서 결과 확인
```

---

## 기대 효과

- 사용자가 견적 요청서를 작성할 때 더 좋은 판단을 할 수 있습니다.
- 관리자가 다음 달 구독 매출을 미리 예측할 수 있습니다.
- 사용자에게 더 잘 맞는 전문가를 빠르게 추천할 수 있습니다.
- 사람이 직접 확인해야 하는 시간을 줄이고, 서비스 이용 흐름을 더 편하게 만듭니다.
