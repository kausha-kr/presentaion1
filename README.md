## AI 서비스

### 1. 견적 요청 AI 검토

견적 요청 AI 검토는 사용자가 견적 요청을 작성할 때, AI가 요청 내용을 자동으로 분석하는 기능입니다.

AI는 사용자가 입력한 제목과 내용을 바탕으로 해당 글이 견적 요청으로 적절한지 판단합니다.  
내용이 너무 짧거나 의미가 부족한 경우에는 사용자가 다시 작성할 수 있도록 도와줍니다.

이를 통해 잘못 작성된 요청이나 의미 없는 요청을 줄이고, 전문가가 더 정확한 견적 요청을 받을 수 있도록 했습니다.

**대표 코드**

```javascript
const result = await service.classifyEstimation(title, content);
```

```java
webClient.post()
    .uri("/estimation/estimation-regist")
    .bodyValue(request);
```

```python
@app.post("/estimation/estimation-regist")
def predict_estimation(request: ClassificationRequest):
    return classify(request)
```

---

### 2. 전문가 추천 AI

전문가 추천 AI는 사용자의 정보와 관심 분야를 바탕으로 적절한 전문가를 추천하는 기능입니다.

사용자의 지역, 관심 카테고리, 태그 정보를 분석해 관련성이 높은 전문가를 우선적으로 보여줍니다.  
사용자는 직접 전문가를 일일이 찾지 않아도 자신에게 맞는 전문가를 쉽게 확인할 수 있습니다.

이 기능은 사용자와 전문가를 더 빠르게 연결하기 위해 구현했습니다.

**대표 코드**

```java
webClient.post()
    .uri("/recommendation/experts")
    .bodyValue(request);
```

```javascript
const experts = await loadAiExperts(memberId);
renderAiExperts(experts);
```

```python
@app.post("/recommendation/experts")
def recommend_experts(request: RecommendationRequest):
    return {"recommendations": result}
```

---

### 3. 관리자 매출 예측 AI

관리자 매출 예측 AI는 서비스 운영 데이터를 기반으로 예상 월 매출을 예측하는 기능입니다.

회원 수, 유료 구독자 수, 신규 가입자 수, 결제 성공률, 마케팅 비용 등의 데이터를 AI 서버로 전달합니다.  
AI는 이 데이터를 분석해 예상 월 매출을 반환하고, 관리자는 이를 운영 지표로 참고할 수 있습니다.

이 기능을 통해 관리자는 단순 통계 확인을 넘어 앞으로의 서비스 흐름을 예측할 수 있습니다.

**대표 코드**

```java
webClient.post()
    .uri("/regression/predict")
    .bodyValue(body);
```

```javascript
const prediction = await adminService.predictRevenue(payload);
renderRevenuePrediction(prediction);
```

```python
@app.post("/regression/predict")
def predict_revenue(request: RegressionRequest):
    return RegressionResponse(expectedMonthlyRevenue=prediction)
```

---


## 구현 포인트


- FastAPI 기반 AI 서버를 Spring 서버와 연동해 견적 검토, 전문가 추천, 매출 예측 기능을 구현했습니다.
- Cloudflare Tunnel을 이용해 배포된 Spring 서버가 로컬 AI 서버와 통신할 수 있도록 연결했습니다.
