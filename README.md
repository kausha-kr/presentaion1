# GlobalGates AI 서비스

GlobalGates의 AI 서비스는 사용자가 입력한 정보와 서비스 데이터를 분석해 사람이 더 빠르게 판단할 수 있도록 돕는 기능입니다. AI가 모든 결정을 대신하는 것이 아니라, 견적 승인 가능성, 다음 달 예상 매출, 전문가 추천, 채팅 추천 문구처럼 판단에 참고할 수 있는 결과를 제공합니다.

## 프로젝트 링크

- GlobalGates 프로젝트: https://github.com/kausha-kr/globalgates
- 상세 포트폴리오: https://app.notion.com/p/3b2a62a7b858811393def3e5adb8bf73
- 담당 기능 정리: https://github.com/kausha-kr/presentation

## 기획 배경

GlobalGates는 중소기업의 글로벌 비즈니스 연결을 돕는 플랫폼입니다. 해외 진출을 준비하는 기업은 시장 정보, 전문가 탐색, 견적 요청, 상담 과정을 각각 따로 처리해야 하는 경우가 많습니다. 이 과정에서 사용자가 더 빠르게 판단할 수 있도록 데이터 분석과 AI 추천 기능을 접목했습니다.

### KOTRA 해외진출기업 데이터 분석

공공데이터포털의 KOTRA 해외진출기업 데이터를 활용해 국내 기업의 해외 진출 현황을 분석했습니다.

분석 목적은 국가별, 업종별, 지역별 진출 특성을 파악하고, 글로벌 판로 개척 플랫폼의 필요성을 설명하는 것이었습니다.

확인한 내용:

- 국가별 해외 진출 기업 분포
- 업종별 해외 진출 특성
- 지역별 산업 집중 현황
- 투자 형태 및 진출 유형 분석
- 데이터 품질 및 문자열 이상치 탐색

최종 인사이트는 해외 진출 기업이 특정 국가와 업종에 집중되어 있어 신규 해외 진출을 준비하는 기업이 시장 정보와 파트너 탐색에 어려움을 겪을 수 있다는 점입니다. 따라서 기업 간 정보를 피드 기반으로 공유하고, 해외 바이어와 전문가를 연결하는 비즈니스 플랫폼이 필요하다고 판단했습니다.

## AI 기능

### 1. 전문가 견적 승인 및 거절 예측

사용자가 견적 요청서를 작성하면 AI가 요청 내용을 보고 전문가가 이 요청을 승인할 가능성이 높은지 예측합니다.

사용한 모델은 `Naive Bayes`입니다.

주요 기능:

- 견적 요청 내용 분석
- 승인 가능성 예측
- 거절 가능성 예측
- 견적 요청 작성 단계에서 참고 결과 제공

대표 코드:

```python
@app.post("/estimation/estimation-regist", response_model=ClassificationResponse)
def predict_estimation_approval(request: EstimationRequest):
    prediction = estimation_model.predict([request_text])[0]
    return {"prediction": prediction, "confidence": confidence}
```

### 2. 구독자 정보 기반 다음 달 구독 매출 예측

관리자가 현재 구독자 수, 신규 가입자 수, 취소된 구독 수, 마케팅 비용을 입력하면 AI가 다음 달 구독 매출을 예측합니다.

사용한 모델은 `RandomForest`입니다.

주요 기능:

- 무료 회원, 유료 회원 수 분석
- 최근 신규 구독자 수 분석
- 구독 취소 및 만료 데이터 반영
- 다음 달 예상 구독 매출 계산

대표 코드:

```python
@app.post("/regression/predict", response_model=RegressionResponse)
def predict_revenue(request: RegressionRequest):
    prediction = regression_model.predict(input_df)[0]
    return {"expectedMonthlyRevenue": round(float(prediction), 2)}
```

### 3. 개인 수출입 정보 기반 전문가 매칭

사용자의 관심 분야, 지역, 수출입 정보 등을 바탕으로 가장 잘 맞는 전문가를 추천하는 기능입니다.

사용한 방식은 `TF-IDF`와 `벡터 유사도`입니다.

주요 기능:

- 사용자 관심 분야 분석
- 전문가 소개, 지역, 전문 분야 분석
- 사용자와 전문가의 유사도 계산
- 가장 잘 맞는 전문가 목록 추천

대표 코드:

```python
@app.post("/recommendation/experts")
def recommend_experts(request: ExpertRecommendationRequest):
    user_vector = tfidf_vectorizer.transform([user_text])
    similarities = cosine_similarity(user_vector, expert_vectors)
    return {"experts": recommended_experts}
```

### 4. 채팅 추천 문구 생성

사용자가 채팅을 할 때 바로 보낼 수 있는 짧은 추천 문장을 만들어주는 기능입니다.

최근 대화 내용, 대화 단계, 사용자 의도, 원하는 말투, 감정 상태를 보고 자연스러운 문장 3개를 추천합니다.

사용 기술:

- LLM
- PromptTemplate
- FastAPI
- OpenAI API

대표 코드:

```python
prompt_template = PromptTemplate.from_template(template)
prompt = prompt_template.format(**sample.to_dict())
```

## 사용한 AI 기술

| 기능 | 사용 기술 | 설명 |
| --- | --- | --- |
| 전문가 견적 승인 및 거절 예측 | Naive Bayes | 문장을 보고 승인/거절 가능성을 분류 |
| 다음 달 구독 매출 예측 | RandomForest | 여러 조건을 기반으로 숫자 결과 예측 |
| 전문가 매칭 | TF-IDF, 벡터 유사도 | 사용자 정보와 전문가 정보를 벡터화해 유사도 비교 |
| 채팅 추천 문구 | LLM, PromptTemplate | 대화 맥락을 기반으로 바로 사용할 문장 추천 |

## AI 서비스 흐름

```text
사용자 또는 관리자 입력
-> FastAPI AI 서버로 데이터 전달
-> AI 모델이 데이터 분석
-> 예측 결과 또는 추천 결과 반환
-> Spring Boot 서비스와 웹 화면에서 결과 확인
```

## 기대 효과

- 사용자가 견적 요청서를 작성할 때 더 좋은 판단을 할 수 있습니다.
- 관리자가 다음 달 구독 매출을 미리 예측할 수 있습니다.
- 사용자에게 더 잘 맞는 전문가를 빠르게 추천할 수 있습니다.
- 채팅 중 바로 보낼 수 있는 추천 문구로 상담 흐름을 더 자연스럽게 만들 수 있습니다.
- 사람이 직접 확인해야 하는 시간을 줄이고 서비스 이용 흐름을 더 편하게 만듭니다.
