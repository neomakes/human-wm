# 실행 계획: 기초 분석에서 Personal Intelligence까지

**작성일**: 2025-12-08  
**기간**: 12주 (3개월)  
**목표**: Behavior 데이터 → Multi-modal 통합 → 인과추론 기반 개인화 추천

---

## 📊 프로젝트 타임라인 전체 맵

```
Week 1-2: Phase 1 (현재)
┌─────────────────────────────────────────────┐
│ ✅ Baseline VRAE 훈련 완료                   │
│ ✅ 모델 성능 5개 실험 평가 완료              │
│ 🔄 Tiny/Compact 모델 검증 중 (이번 주)      │
│ 📋 Mind UI 설계 준비 시작                    │
└─────────────────────────────────────────────┘
         ↓ (2주 후)

Week 3-4: Phase 2 (단기)
┌─────────────────────────────────────────────┐
│ ✅ Mind 데이터 수집 UI 완성                  │
│ ✅ Body 데이터 API 연동 (Oura, HealthKit)   │
│ ✅ 다중 모달 정규화 파이프라인 구축          │
│ ✅ 베타 사용자 100명 리크루트                │
│ ⚠️ 초기 Mind/Body 데이터 수집 시작           │
└─────────────────────────────────────────────┘
         ↓ (4주 후)

Week 5-9: Phase 3 (중기)
┌─────────────────────────────────────────────┐
│ 🔄 Multi-modal VRAE 구현                     │
│    - Mind Encoder (GRU, 32D)                │
│    - Behavior Encoder 확장                  │
│    - Body Encoder (GRU, 32D)                │
│ 🔄 Cross-modal Attention 학습               │
│ 📊 개인별 125×125×125 궤적 생성             │
│ 📈 100명 사용자 패턴 분석                   │
└─────────────────────────────────────────────┘
         ↓ (9주 후)

Week 10-12: Phase 4 (장기)
┌─────────────────────────────────────────────┐
│ 🧠 Causal Inference Engine 구현              │
│    - Causal Forest (DoWhy)                  │
│    - CATE (개인별 처리 효과)                │
│ 💡 개인화 추천 엔진                          │
│    - Daily 추천                             │
│    - Weekly 패턴 분석                       │
│    - Monthly 트렌드 경고                     │
│ 🚀 1000명 사용자 스케일                      │
│ 💰 수익화 가능 기능 준비                     │
└─────────────────────────────────────────────┘
```

---

## 🎯 Phase 1: 행동 패턴 단독 학습 (현재, 2주)

### 목표
- Tiny/Compact 모델 성능 검증
- Behavior VRAE 최적화 완료
- 기초 분석 표준 최종 확정

### 작업 항목

#### 1.1 모델 훈련 및 검증
```yaml
일정: 이번 주 (Week 1)

작업:
  - Tiny 모델 훈련
    └─ h=128, l=1, z=8/16/16 (338K params)
    └─ 예상 시간: 2-3시간
    └─ 목표 Loss: < 10.4 (Baseline 대비 +1%)
    
  - Compact 모델 훈련
    └─ h=128, l=2, z=8/16/16 (518K params)
    └─ 예상 시간: 3-4시간
    └─ 목표 Loss: < 10.3
    
  - 검증 메트릭
    └─ Val Loss 비교
    └─ 훈련 시간 비교 (Baseline: 50분)
    └─ 메모리 사용량 비교
    └─ 생성 궤적 다양성 평가
```

#### 1.2 기초 분석 최종화
```yaml
일정: Week 1 중반

작업:
  - [01] DataPreprocessing.ipynb 검증
    └─ 현재: 899명 × 1000 timesteps ✓
    └─ 마스킹율: 63.4% (예상) ✓
    
  - 데이터 통계 최종화
    └─ 행동 다양성: 8가지 분류
    └─ 상태 다양성: 5가지 기분
    └─ 행동-상태 cross-tabulation 추가
    
  - 이상치 분석 (선택)
    └─ Isolation Forest로 이상 사용자 식별
    └─ "매일 10시간 운동" 같은 극단값 제거?
```

#### 1.3 Phase 2 준비
```yaml
일정: Week 1-2

작업:
  - Mind 데이터 수집 설계
    └─ EMA 설문 최종안 5문항
    └─ 기분(Mood), 스트레스, 집중력, 동기, 소진도
    └─ 빈도: 아침/점심/저녁 (일 3회)
    └─ UI 목업 (Figma)
    
  - Body 데이터 요구사항 정의
    └─ Oura Ring: 필요한 지표?
       └─ HRV, Sleep score, Readiness
    └─ Apple HealthKit: 필요한 지표?
       └─ Heart rate, Active calories, Sleep duration
    
  - 규제 검토 초안
    └─ GDPR/CCPA 프라이버시 정책
    └─ 데이터 보관/삭제 정책
    └─ 사용자 동의서
```

### 산출물

- ✅ 최적 모델 선택 (Tiny vs Compact vs Baseline)
- ✅ [03] DataAnalysisSignificance.md (현재 작성 중)
- ✅ [04] ExecutionPlan.md (현재 작성 중)
- 📋 Mind 데이터 수집 UI 설계안
- 📋 규제 준수 체크리스트

---

## 🎯 Phase 2: 다중 모달 데이터 수집 (2주, Week 3-4)

### 목표
- Mind + Body 데이터 수집 파이프라인 구축
- 100명 베타 사용자 리크루트
- Multi-modal 데이터 정규화 표준 확립

### 작업 항목

#### 2.1 Mind 데이터 수집 시스템
```yaml
일정: Week 3 (1주)

작업:
  1️⃣ EMA 설문 애플리케이션
     - React Native로 MVP 모바일 앱 개발
     - 설문 5문항 (1분/회)
       ├─ "현재 기분은?" (1-5 이모지)
       ├─ "스트레스 수준?" (1-5 슬라이더)
       ├─ "집중력은?" (1-5)
       ├─ "동기부여는?" (1-5)
       └─ "소진도(Burnout)?" (1-5)
     
     - 푸시 알림 (아침 8시, 점심 12시, 저녁 6시)
     - 응답 저장 (Firebase)
     
  2️⃣ 데이터 수집 백엔드
     - FastAPI 엔드포인트
       └─ POST /api/ema/submit
       └─ 인증, 데이터 검증, 저장
     
     - 실시간 모니터링
       └─ "오늘 80% 사용자가 응답"
  
  3️⃣ 품질 관리
     - "3일 이상 미응답" 사용자 추적
     - 응답 시간 패턴 분석
     - 데이터 완전성 95% 목표
```

#### 2.2 Body 데이터 API 연동
```yaml
일정: Week 3-4 (1.5주)

작업:
  1️⃣ Oura Ring API 연동
     - 사용자 인증 (OAuth 2.0)
     - 매일 동기화 (하루 한 번, 자정)
     - 수집 지표:
       ├─ HRV (Heart Rate Variability)
       ├─ Sleep Score
       ├─ Readiness Score
       └─ Body Temperature Trend
  
  2️⃣ Apple HealthKit 연동
     - HealthKit 권한 요청
     - 매일 동기화
     - 수집 지표:
       ├─ Heart Rate (평균/최대)
       ├─ Respiratory Rate
       ├─ Sleep Duration & Quality
       └─ Active Energy (칼로리)
  
  3️⃣ 데이터 저장소 설계
     - PostgreSQL 스키마
       └─ users: 사용자 정보
       └─ mind_ema: EMA 응답 (일 3회)
       └─ body_oura: Oura 데이터 (일 1회)
       └─ body_healthkit: HealthKit 데이터 (실시간)
       └─ behavior: 행동 데이터 (기존)
     
     - Redis 캐시
       └─ 최근 30일 데이터 캐싱
```

#### 2.3 데이터 정규화 파이프라인
```yaml
일정: Week 4 (1주)

작업:
  1️⃣ 비동기 다중 빈도 데이터 동기화
  
     입력:
       - Mind: 일 3회 (아침/점심/저녁)
       - Body: 일 1회 (자정 기준)
       - Behavior: 일 1회 (기존)
     
     정규화 단계:
     
     ┌─────────────────────────────────────┐
     │ 1단계: 데이터 수집 및 검증           │
     │   - 결측치 확인                      │
     │   - 이상치 필터링                    │
     │   - 시간대별 정렬                    │
     └─────────────────────────────────────┘
                    ↓
     ┌─────────────────────────────────────┐
     │ 2단계: 일단위 정규화                 │
     │                                     │
     │ Mind:                               │
     │   - 일 3회 점수 → 평균 + 최대값      │
     │   - 예: mood 아침(4) + 점심(3) +    │
     │     저녁(5) → mean=4.0, max=5      │
     │                                     │
     │ Body:                               │
     │   - 시간별 데이터 → 일단위 통계      │
     │   - HRV: 평균, 최소, 최대, 표준편차│
     │   - Sleep: 시간, 깊이 점수          │
     │                                     │
     │ Behavior:                           │
     │   - 기존 일단위 (변화 없음)          │
     │                                     │
     └─────────────────────────────────────┘
                    ↓
     ┌─────────────────────────────────────┐
     │ 3단계: 피처 생성                     │
     │   - Mind Trend: 하루 중 변화        │
     │   - Body Trend: 주간 변화           │
     │   - Behavior Intensity: 활동량      │
     └─────────────────────────────────────┘
                    ↓
     ┌─────────────────────────────────────┐
     │ 4단계: 스케일링 및 마스킹             │
     │   - 0-1 정규화 (기초 분석 표준 유지) │
     │   - 마스크 생성: 결측 부위 표시      │
     │                                     │
     │ 결과:                               │
     │   (user, day, features)             │
     │   where features = [              │
     │     mind_avg, mind_trend,           │
     │     body_hrv, body_sleep,           │
     │     behavior_steps, behavior_mood   │
     │   ]                                 │
     └─────────────────────────────────────┘
  
  2️⃣ 스크립트 개발
     - normalize_multimodal.py
     - 입력: Firebase + Oura + HealthKit 데이터
     - 출력: (N users, 365 days, 25 features) 텐서
     - 마스킹: (N users, 365 days, 1) 마스크
     
     실행:
     ```bash
     python scripts/normalize_multimodal.py \
       --start-date 2025-01-01 \
       --end-date 2025-12-31 \
       --output data/multimodal_data.npz
     ```
  
  3️⃣ 품질 검증
     - 마스킹율 통계 (기초 분석처럼)
     - 사용자별 커버리지 분석
     - 피처별 분포 확인
     - 시간별 결측 패턴
```

#### 2.4 베타 사용자 리크루트
```yaml
일정: Week 4 내내 (병렬 진행)

작업:
  - 리크루트 대상: 100명
  - 모집 채널:
    ├─ 페이스북 그룹 (생산성 커뮤니티)
    ├─ Reddit r/productivity
    ├─ 개인 네트워크
    └─ Product Hunt
  
  - 포함 기준:
    ├─ 적어도 Oura Ring 또는 Apple Watch 보유
    ├─ 매일 앱 사용 의지
    ├─ 3개월 데이터 수집 동의
  
  - 인센티브:
    ├─ 무료 앱 제공
    ├─ 월간 리포트 제공
    └─ 최우수 사용자 상품권 ($50)
```

### 산출물

- ✅ Mind EMA 수집 앱 (MVP, React Native)
- ✅ Body API 연동 (Oura + HealthKit)
- ✅ 다중 모달 정규화 파이프라인 (`normalize_multimodal.py`)
- ✅ 100명 베타 사용자 데이터 (첫 2주)
- 📊 Phase 2 완료 리포트
  - 데이터 수집 완전성
  - 피처별 분포
  - 사용자별 커버리지

---

## 🎯 Phase 3: Multi-modal 패턴 발견 (6주, Week 5-10)

### 목표
- Mind-Behavior-Body 통합 패턴 학습
- Cross-modal Attention으로 상호작용 발견
- 개인별 125³ 궤적 생성 및 분석

### 작업 항목

#### 3.1 Multi-modal VRAE 아키텍처
```yaml
일정: Week 5-6 (2주)

설계:
  
  ┌──────────────────────────────────────────────────────┐
  │         Multi-Modal VRAE 아키텍처                     │
  └──────────────────────────────────────────────────────┘
  
  입력 데이터:
  ├─ Mind: (N, T, 5) → [mood, stress, focus, motivation, burnout]
  ├─ Behavior: (N, T, 10) → [steps, workout_type, location, ...]
  └─ Body: (N, T, 8) → [HRV, sleep, HR, readiness, ...]
  
  Step 1: 모달별 Encoder
  
  Mind Encoder (GRU):
    Input: (N, T, 5)
    ├─ Embedding (categorical features)
    ├─ GRU (bidirectional, hidden=64)
    └─ Attention pooling
    Output: (N, 64)
  
  Behavior Encoder (GRU) ← 기존 VRAE 확장:
    Input: (N, T, 10) ← 기존 (N, T, 10)
    ├─ Embedding
    ├─ GRU (bidirectional, hidden=128) ← 기존 hidden_dim=256
    └─ Attention pooling ← 기존 마스킹
    Output: (N, 128)
  
  Body Encoder (GRU):
    Input: (N, T, 8)
    ├─ Embedding
    ├─ GRU (bidirectional, hidden=64)
    └─ Attention pooling
    Output: (N, 64)
  
  Step 2: Shared Latent Space
  
  Concatenate 및 Projection:
    Encoder outputs ← [64 + 128 + 64] = 256D
    ↓
    FC layers (256 → 128)
    ↓
    Latent distributions:
    ├─ z_wellness: N(μ_w, σ_w) [32D] ← 종합 웰니스
    ├─ z_mind: N(μ_m, σ_m) [16D] ← 정신 특화
    ├─ z_behavior: N(μ_b, σ_b) [32D] ← 행동 특화 (기존 z_b 확장)
    └─ z_body: N(μ_bd, σ_bd) [16D] ← 신체 특화
  
  Step 3: Decoder
  
  Decode from [z_wellness, z_mind, z_behavior, z_body]:
    ↓
    FC expansion (96D → 128)
    ↓
    GRU (bidirectional, hidden=128)
    ↓
    Outputs:
    ├─ Mind reconstruction: (N, T, 5)
    ├─ Behavior reconstruction: (N, T, 10)
    └─ Body reconstruction: (N, T, 8)
  
  Step 4: Cross-Modal Attention (신규)
  
  마음 → 행동 영향:
    Query: z_mind
    Key/Value: Behavior encoder outputs
    Result: Interaction weights
    Meaning: "마음이 행동 선택에 미치는 영향"
  
  행동 → 신체 영향:
    Query: z_behavior
    Key/Value: Body encoder outputs
    Result: Interaction weights
    Meaning: "행동이 신체 반응에 미치는 영향"
  
  신체 → 마음 영향:
    Query: z_body
    Key/Value: Mind encoder outputs
    Result: Interaction weights
    Meaning: "신체 상태가 마음에 미치는 영향"

작업:
  - models/multimodal_vrae.py 구현
    ├─ MindEncoder, BehaviorEncoder, BodyEncoder
    ├─ SharedLatentSpace
    ├─ MultiModalDecoder
    └─ CrossModalAttention
  
  - 약 1500-2000줄 (기존 VRAE 867줄 × 1.5-2배)
  - 테스트 케이스: 간단한 합성 데이터로 shape 확인
```

#### 3.2 학습 및 손실 함수
```yaml
일정: Week 6-7 (1.5주)

손실 함수:

L_total = w_vae * L_vae + w_cross * L_cross + w_recon * L_recon

1️⃣ VAE 손실 (기존과 동일)
   L_vae = (L_recon_mind + L_recon_behavior + L_recon_body) + β * L_KL
   - 모든 모달의 재구성 손실
   - KL divergence (3개 모달의 합)

2️⃣ Cross-Modal 손실 (신규)
   L_cross = attention_weight_consistency
   - 마음-행동-신체 상호작용 강도 계산
   - 약한 상호작용은 정규화 (과적합 방지)

3️⃣ 재구성 손실 (개선)
   L_recon = MSE(mind_pred, mind_true) + Huber(behavior_pred, behavior_true) + MSE(body_pred, body_true)
   - 모달별 최적 메트릭 (행동은 Huber, 나머지 MSE)

권장 가중치:
  w_vae = 1.0
  w_cross = 0.3 (Cross-modal 학습 시작)
  w_recon = 0.5 (모달별 정확도)

훈련 파라미터:
  learning_rate = 0.0003
  batch_size = 32 (사용자 수)
  epochs = 200 (더 복잡한 패턴 학습)
  kl_annealing_end = 50 (더 긴 annealing)
```

#### 3.3 개인별 패턴 분석
```yaml
일정: Week 8-10 (3주)

작업:
  
  1️⃣ 100명 사용자 대상 궤적 생성
  
     For each user:
       - 지난 30일 데이터로 encoder 실행
       - μ, σ 계산
       - z_wellness, z_mind, z_behavior, z_body 샘플링
         └─ K=5개 (각 모달) → 5×5×5×5 = 625개 조합
       
       - 각 조합별로 rollout
         └─ T=30 (다음 30일)
         └─ Policy: 상태 + 마음 → 행동 예측
         └─ Transition: 행동 → 다음 상태
       
       - 결과: 625개 미래 시나리오
         └─ "당신은 이런 식으로도 행동할 수 있어"
  
  2️⃣ 개인별 패턴 특성화
  
     각 사용자별로 계산:
     
     Mind Profile:
       - 평균 기분 점수
       - 기분 변동성 (std)
       - 스트레스 추세 (linear fit)
       - "당신은 평균적으로 행복한가 슬픈가?"
     
     Behavior Profile:
       - 운동 강도 선호도
       - 활동 시간대 (아침형 vs 밤형)
       - 일관성 (주간 vs 주말 차이)
       - "당신은 규칙적인가 자유로운가?"
     
     Body Profile:
       - HRV 추세 (회복 능력)
       - 수면 품질 추세
       - 활동-회복 균형
       - "당신의 신체는 잘 회복되는가?"
     
     Cross-Modal Patterns:
       - Mind-Behavior: "마음이 안 좋을 때 어떤 행동?"
       - Behavior-Body: "운동 후 신체 회복 시간?"
       - Body-Mind: "수면 부족이 기분에 미치는 영향?"
       - "당신의 고유한 마음-행동-신체 사이클"
  
  3️⃣ 클러스터링 및 비교
  
     100명 사용자를 z_wellness 기준으로 분류:
     
     Cluster 1 (행복한 사람들, ~30명):
       - 평균 mood > 4.0
       - 높은 운동 강도
       - 우수한 수면 품질
       - 특징: "당신은 이 그룹과 비슷해요"
     
     Cluster 2 (소진된 사람들, ~20명):
       - 평균 burnout > 3.5
       - 불규칙한 운동 패턴
       - 불안정한 수면
       - 특징: "주의: 번아웃 위험"
     
     Cluster 3 (활동적인 사람들, ~25명):
       - 높은 운동 빈도
       - 일관된 행동 패턴
       - 강한 신체 신호
       - 특징: "당신은 규칙적이에요"
     
     Cluster 4 (회복자들, ~25명):
       - 낮은-중간 활동 수준
       - 차분한 마음 상태
       - 우수한 회복 신호
       - 특징: "당신은 자신을 잘 돌봐요"
     
     시각화:
     - t-SNE: 4D latent space → 2D
     - 클러스터 색상 구분
     - 개인별 위치 표시
     - "당신은 여기에 있어요"
```

#### 3.4 검증 및 평가
```yaml
일정: Week 9-10 (2주)

메트릭:

1️⃣ 모델 성능
   - Reconstruction Loss: 각 모달별 MAE/RMSE
   - KL Divergence: 3개 모달 합계
   - Cross-modal Attention Weight: 0-1 범위 (균형?)
   
   예시:
   ┌─────────────────────────────────────────┐
   │ Mind Reconstruction MAE: 0.23            │
   │ (기분 1-5 척도에서 평균 0.23 오차)      │
   │                                         │
   │ Behavior Reconstruction MAE: 15.2       │
   │ (단계 수, 평균 15 단계 오차)            │
   │                                         │
   │ Body Reconstruction MAE: 8.5            │
   │ (HRV bpm, 평균 8.5 오차)                │
   │                                         │
   │ Cross-modal Attention Weights:          │
   │ - Mind→Behavior: avg 0.45               │
   │ - Behavior→Body: avg 0.62               │
   │ - Body→Mind: avg 0.38                   │
   │ → 행동이 신체에 가장 강한 영향         │
   └─────────────────────────────────────────┘

2️⃣ 궤적 품질
   - Trajectory Diversity: 625개 궤적이 충분히 다른가?
     └─ Variance 계산 (행동 차원별)
   
   - Coverage: 생성된 궤적이 실제 행동 범위를 커버?
     └─ "운동 0시간부터 2시간까지 모두 생성?"
   
   - Consistency: 같은 초기 상태에서 유사한 궤적?
     └─ "마음이 좋으면 항상 운동하는가?"

3️⃣ 사용자 피드백 (정성적)
   - "이 분석이 맞나요?"
   - "당신이 어떤 패턴을 발견했나요?"
   - NPS (Net Promoter Score)
```

### 산출물

- ✅ Multi-modal VRAE 모델 (훈련됨)
- ✅ 100명 사용자 패턴 분석 리포트
  - 4개 클러스터 프로필
  - Cross-modal Attention 분석
  - 개인별 125³ 궤적 시각화
- 📊 Phase 3 완료 리포트
  - 모델 성능 메트릭
  - 궤적 다양성 분석
  - 사용자 만족도 조사

---

## 🎯 Phase 4: 인과추론 및 개인화 (4주, Week 11-12+)

### 목표
- 개인별 인과관계 발견
- 맞춤 추천 엔진 구축
- 1,000명 스케일 준비

### 작업 항목

#### 4.1 Causal Inference 엔진
```yaml
일정: Week 11 (1주)

목표: "당신에게 무엇이 효과 있는가?"

라이브러리: DoWhy + EconML

Step 1: Causal Forest로 CATE 추정

개인별 인과관계:
  "Sleep이 Productivity에 미치는 효과"
  
  Generic (전체 사용자): +0.35
  ↓
  Your personalized effect: ?
  
  Causal Forest:
    Input: 
      - X (features): mind, behavior, body
      - T (treatment): sleep_hours (continuous)
      - Y (outcome): productivity_score
    
    Output: 
      - CATE (Conditional Average Treatment Effect)
      - "당신은 sleep에 +0.45 sensitive"
      - "당신의 동료 B는 +0.20 sensitive"
  
  의미:
    "같은 1시간 수면 추가 시,
     당신의 생산성은 +45% 향상,
     평균적으로는 +35% 향상"

Step 2: Causal DAG 구성

마음-행동-신체 인과 구조:
  
  ┌──────┐     ┌──────────┐     ┌────────┐
  │ 기분  │────→│ 운동 선택  │────→│ 신체 회복│
  └──────┘     └──────────┘     └────────┘
     ▲                              │
     │                              ↓
     └──────────────────────────────┘
     (좋은 신체 상태 → 좋은 기분)

추정하는 인과관계:
  1. 기분 → 운동 강도 (행동 선택 메커니즘)
  2. 운동 → 신체 회복 (생리적 메커니즘)
  3. 신체 회복 → 기분 개선 (피드백 루프)
  4. 기분 → 수면 품질? (심리적 메커니즘)
  5. 스트레스 → 모든 것 (숨겨진 confounder)

각 인과관계별 개인적 효과 크기:
  - User A: 기분→운동 (강함), 신체→기분 (약함)
  - User B: 기분→운동 (약함), 신체→기분 (강함)
  - → 맞춤 추천 전략 다름

Step 3: 구현

```python
from econml.forest import CausalForest
from dowhy import CausalModel

# CATE 추정
causal_forest = CausalForest()
treatment_effect = causal_forest.fit_predict(
    X=features,  # mind, behavior, body
    T=sleep_hours,  # 처리 (수면 시간)
    y=productivity  # 결과 (생산성)
)

# 결과: 개인별 인과 효과
for user_id in range(100):
    effect = treatment_effect[user_id]
    print(f"User {user_id}: +1h sleep → +{effect:.1%} productivity")
```

작업:
  - DoWhy + EconML 통합
  - Causal Forest 모델 훈련
  - 개인별 CATE 계산
  - 신뢰도 구간 (confidence interval) 추정
```

#### 4.2 개인화 추천 엔진
```yaml
일정: Week 11-12 (2주)

설계:

Recommendation Engine = CATE + Intervention Simulation + Optimization

┌────────────────────────────────────────────────────┐
│ 개인화 추천 엔진 아키텍처                            │
└────────────────────────────────────────────────────┘

입력: 현재 상태 (mind_today, body_today, behavior_today)

Step 1: 인과효과 평가
  
  현재 상태에서:
  - 기분: 4/5 (좋음)
  - 스트레스: 2/5 (낮음)
  - 신체 회복: 7/10 (우수)
  - 운동 강도: 30분 (중간)
  
  YOUR causal effects:
  - Sleep → Productivity: +0.45
  - Exercise → HRV: +0.38
  - Stress → Sleep: -0.52

Step 2: 개입 시뮬레이션
  
  Intervention 1: "내일 운동을 2시간 하면?"
    - Behavior: +1.5h exercise
    - Expected Body: HRV +2.8, fatigue +0.5
    - Expected Mind: mood +0.3 (indirect)
    - ROI: 중간 (피로 증가)
  
  Intervention 2: "스트레스를 50% 줄이면?"
    - Mind: stress -2.5
    - Expected Sleep: +1.2h (당신은 스트레스에 민감)
    - Expected Productivity: +0.4 (당신에겐 큰 영향)
    - ROI: 높음 (최우선)
  
  Intervention 3: "명상 30분"
    - Mind: stress -0.8, focus +0.4
    - Expected Behavior: productivity +0.2
    - ROI: 쉬움 (즉시 효과)

Step 3: 추천 최적화
  
  현재:
    ├─ mood: 4/5
    ├─ stress: 2/5 (낮음, 다행)
    ├─ focus: 3/5 (중간)
    └─ energy: 5/5 (높음)
  
  분석:
    "당신은 에너지가 높고 스트레스가 낮아요.
     이 상태에서는 도전적 작업이 효과적입니다."
  
  추천:
    🎯 Today (일일):
      1. ✅ 어려운 프로젝트에 집중 (9-12시)
      2. ✅ 30분 운동 (12시 전)
      3. ✅ 점심 후 낮잠 불필요 (에너지 충분)
    
    📅 This Week (주간):
      1. ✅ 목요일에 집중력 낮아지는 패턴 있음
         → 화요일에 중요 작업 몰아서 처리
      2. ✅ 주말 운동이 월요일 생산성에 +0.4 효과
         → 토요일 운동 추가 권장
      3. ✅ 현재 스트레스 낮음 = 새로운 도전 기회
         → 새 프로젝트 시작하기 좋은 시점
    
    📊 This Month (월간):
      1. ⚠️ 번아웃 위험 신호: 아직 없음
      2. 📈 생산성 트렌드: 상승 (+8% 지난달 대비)
      3. 💡 최적 상태: 주 2회 도달 (목표는 주 3회)
         → 수면 시간 +0.5h, 스트레스 -0.3이면 달성 가능

Step 4: 개입 선택 및 추적
  
  추천에서 선택:
    "30분 운동" → 선택
    ↓
  추적:
    - 운동 전: mood 4, HRV 52
    - 운동 후: mood 4.3 (+0.3), HRV 58 (+6)
    ↓
  학습:
    "효과 검증됨: 당신에게 운동 직후 +0.3 mood 향상"
    (기존 예측 CATE: +0.25였음 → 실제 +0.3)

작업:
  - InterventionEngine 클래스 구현
    ├─ simulate_intervention()
    ├─ estimate_roi()
    └─ rank_recommendations()
  
  - RecommendationOptimizer 구현
    ├─ current_state 분석
    ├─ 최적 개입 5개 순위 결정
    └─ 개인의 선호도 고려 (어려운 작업 회피?)
  
  - FeedbackLoop 구현
    ├─ 추천 선택 기록
    ├─ 결과 측정
    └─ CATE 재조정 (online learning)
```

#### 4.3 추천 제공 채널
```yaml
일정: Week 12 (1주)

Deployment channels:

1️⃣ 모바일 앱
   - Daily: 푸시 알림 (아침 8시)
     "오늘의 추천: 집중 작업 하기 좋은 날!"
   
   - Weekly: 요약 리포트 (매주 월요일)
     "지난주 생산성 +8%, 계속 운동해요!"
   
   - Monthly: 깊이 있는 분석
     "당신은 월요일에 약한 패턴. 이유는?"

2️⃣ 웹 대시보드
   - Real-time 상태 모니터링
   - 인과관계 지도 시각화
   - 미래 30일 예측
   - 개입 시뮬레이터 (대화형)
     "만약 내일 3시간만 자면?"

3️⃣ 메일 리포트
   - 주간 요약
   - 월간 심화 분석
   - 새로운 패턴 발견 공지

4️⃣ API (B2B 파트너)
   - 회사의 wellness 프로그램과 통합
   - 팀 대시보드 (개별 정보 보호)
   - 부서별 생산성 트렌드
```

### 산출물

- ✅ Causal Inference Engine (DoWhy/EconML)
- ✅ 개인화 추천 엔진 (InterventionOptimizer)
- ✅ 100명 사용자 개인별 인과 효과 지도
- ✅ 모바일 앱 + 웹 대시보드 (추천 시각화)
- 📊 Phase 4 완료 리포트
  - CATE 분포 분석
  - 추천 정확도 (사후 검증)
  - 사용자 만족도 (NPS 점수)

---

## 📋 전체 타임라인 요약

| Phase | 기간 | 핵심 산출물 | 사용자 수 |
|-------|------|-----------|---------|
| **1** | 2주 | Behavior VRAE 최적화 | - |
| **2** | 2주 | Mind/Body 데이터 수집 파이프라인 | 100명 (시작) |
| **3** | 6주 | Multi-modal VRAE, 패턴 분석 | 100명 |
| **4** | 4주+ | 인과추론 + 추천 엔진 | 1,000명 준비 |

**총 12주 = 3개월**

---

## 💰 예상 리소스 요구사항

### 인력

```
Phase 1 (2주):
  - ML Engineer: 100% (모델 검증)
  - Data Scientist: 50% (분석)

Phase 2 (2주):
  - Full-stack Developer: 100% (앱 + 백엔드)
  - Data Engineer: 100% (데이터 파이프라인)
  - ML Engineer: 50% (지원)

Phase 3 (6주):
  - ML Engineer: 100% (Multi-modal VRAE)
  - Data Scientist: 100% (패턴 분석)
  - Full-stack Developer: 50% (시각화 UI)

Phase 4 (4주):
  - ML Engineer: 100% (인과추론)
  - Data Scientist: 100% (CATE 분석)
  - Full-stack Developer: 100% (추천 엔진 API)

총 투입: ~5.5 FTE (12주)
```

### 기술 스택

```
Backend:
  - FastAPI (Python)
  - PostgreSQL (데이터)
  - Redis (캐시)
  - Celery (비동기 작업)

ML:
  - MLX (모델 훈련)
  - DoWhy (인과추론)
  - EconML (CATE)
  - scikit-learn (기본 ML)

Frontend:
  - React Native (모바일)
  - Next.js (웹)
  - Plotly (시각화)

Infrastructure:
  - Docker (컨테이너)
  - AWS/GCP (클라우드)
  - GitHub (버전 관리)

Cost (12주):
  - AWS compute: ~$3,000
  - Oura API: ~$500 (100명 × 3개월)
  - 총: ~$4,000
```

---

## ✅ 체크리스트

### Phase 1
- [ ] Tiny 모델 훈련 완료
- [ ] Compact 모델 훈련 완료
- [ ] 최적 모델 선택
- [ ] Mind UI 설계 완료
- [ ] 규제 검토 시작

### Phase 2
- [ ] EMA 앱 MVP 개발 (React Native)
- [ ] Oura API 연동 완료
- [ ] HealthKit API 연동 완료
- [ ] 정규화 파이프라인 구축
- [ ] 100명 베타 사용자 리크루트
- [ ] 초기 데이터 수집 (2주)

### Phase 3
- [ ] Multi-modal VRAE 구현
- [ ] 모델 훈련 (200 에포크)
- [ ] 100명 패턴 분석 완료
- [ ] 클러스터링 및 시각화
- [ ] 궤적 다양성 검증

### Phase 4
- [ ] Causal Forest 구현
- [ ] 개인별 CATE 계산
- [ ] 추천 엔진 구현
- [ ] 모바일 앱 통합
- [ ] 웹 대시보드 구축
- [ ] 사용자 테스트

---

## 🚀 다음 단계 (이번 주)

1. **Tiny/Compact 모델 훈련 완료**
   ```bash
   python scripts/train.py model.hidden_dim=128 model.num_layers=1 ...
   python scripts/train.py model.hidden_dim=128 model.num_layers=2 ...
   ```

2. **Mind 데이터 UI 설계 시작**
   - Figma 목업 (EMA 설문 인터페이스)
   - 5문항 최종화

3. **Phase 2 준비 계획**
   - React Native 환경 설정
   - FastAPI 엔드포인트 설계
   - 데이터베이스 스키마 설계

