# 🚀 순차 실험 구성 - 사용 가이드

> **작성일**: 2025-12-07  
> **상태**: ✅ 완료 및 테스트됨

---

## 📌 한눈에 보기

### ✨ 새로운 기능
조기 종료 기능과 통합되어, **여러 실험을 순차적으로 자동 실행**할 수 있습니다.

### 🎯 목표
5개의 서로 다른 모델 설정으로 **모델 아키텍처의 영향을 체계적으로 평가**합니다.

### ⏱️ 소요 시간
약 **3시간** (조기 종료 적용, 전체 200 에포크 설정)

---

## 🚀 빠른 시작 (3가지 방법)

### 1️⃣ Python 직접 실행 (가장 간단)
```bash
cd /Users/neo/ACCEL/WienerMachine/Benchmark/FitnessTracker
python scripts/run_experiments.py
```

### 2️⃣ 셸 스크립트 (권장)
```bash
bash scripts/run_all_experiments.sh
```

### 3️⃣ 백그라운드 실행 (밤새 실행)
```bash
nohup python scripts/run_experiments.py > logs/experiments.log 2>&1 &
# 진행 상황 보기
tail -f logs/experiments.log
```

---

## 📊 실험 목록

| # | 이름 | 설정 | 목표 |
|---|------|------|------|
| 1️⃣ | **Baseline** | 256-dim, 2L, latent=16/32/32 | 기본 성능 기준 |
| 2️⃣ | **Deep Model** | 256-dim, **3L**, latent=16/32/32 | 깊이의 영향 평가 |
| 3️⃣ | **Wide Model** | **512-dim**, 2L, latent=16/32/32 | 폭의 영향 평가 |
| 4️⃣ | **Large Latent** | 256-dim, 2L, **latent=32/64/64** | 잠재 차원의 영향 |
| 5️⃣ | **Optimized** | **512-dim, 2L, latent=32/64/64, lr=0.0005** | 최고 성능 추구 |

---

## 📁 생성된 파일

```
scripts/
├─ run_experiments.py          ← 메인 실험 스크립트
├─ quick_test.py               ← 빠른 테스트용 (20 에포크)
└─ run_all_experiments.sh       ← 셸 래퍼

docs/
├─ EXPERIMENTS_GUIDE.md        ← 전체 상세 가이드
├─ QUICK_START_EXPERIMENTS.md  ← 빠른 참조 & 팁
├─ EXPERIMENTS_SUMMARY.md      ← 이 문서
└─ [02] ModelDesign.md         ← 모델 설계 문서

conf/
└─ training/
   └─ default.yaml             ← 조기 종료 설정 추가
```

---

## 🎯 실행 흐름

```
실행 시작 (python scripts/run_experiments.py)
    ↓
┌─ 01_Baseline (최대 200 에포크, 조기 종료시 중단)
│  └─ 조기 종료 시 자동 진행
├─ 02_DeepModel
│  └─ 조기 종료 시 자동 진행
├─ 03_WideModel
│  └─ 조기 종료 시 자동 진행
├─ 04_LargeLatent
│  └─ 조기 종료 시 자동 진행
└─ 05_Optimized
   └─ 조기 종료 시 자동 진행
    ↓
📊 최종 결과 생성 (logs/experiments_results.json)
    ↓
✅ 완료!
```

---

## 📈 예상 소요 시간

### 각 실험별 예상 시간
- **01_Baseline**: ~30분 (60개 에포크 기준)
- **02_DeepModel**: ~35분
- **03_WideModel**: ~40분
- **04_LargeLatent**: ~35분
- **05_Optimized**: ~45분

### 총합
- **조기 종료 적용**: ~185분 (**3시간**)
- **미적용 (200 에포크)**: ~600분 (10시간)

**효율성: 약 3배 시간 단축! 🚀**

---

## 💻 시스템 요구사항

| 항목 | 최소 | 권장 |
|-----|-----|------|
| **메모리** | 8GB | 16GB+ |
| **시간** | 3시간 | 상시 실행 가능 환경 |
| **W&B** | (선택) | 선택 가능 (use_wandb=false 시 로컬만) |

---

## 🔧 커스터마이징

### 실험 추가하기
`scripts/run_experiments.py` 수정:

```python
EXPERIMENTS = [
    # ... 기존 실험들 ...
    {
        "name": "06_CustomExperiment",
        "description": "나의 커스텀 설정",
        "params": {
            "model.hidden_dim": 768,
            "model.num_layers": 4,
            "training.learning_rate": 0.0002,
            # ...
        }
    },
]
```

### 특정 실험만 실행
```bash
# 03_WideModel만 직접 실행
python scripts/train.py \
  model.hidden_dim=512 \
  model.num_layers=2 \
  training.epochs=200
```

### 빠른 테스트 (개발/디버깅)
```bash
python scripts/quick_test.py  # 에포크 20, W&B 미사용, ~5분
```

---

## 📊 결과 확인

### 1. 실시간 모니터링
```bash
# 가장 최근 로그 실시간 보기
tail -f logs/runs/*/train.log

# 모든 에포크 로그 보기
grep "Epoch" logs/runs/*/train.log | tail -30
```

### 2. W&B 대시보드 (실시간)
```
https://wandb.ai/your-username/fitness-tracker
```

각 실험이 별도의 run으로 기록되므로 실시간 메트릭 확인 및 실험 간 비교 가능

### 3. 최종 결과 파일 (JSON)
```bash
# 결과 보기
cat logs/experiments_results.json | python -m json.tool

# 또는 Python으로
python -c "
import json
with open('logs/experiments_results.json') as f:
    results = json.load(f)
    print(f'완료: {results[\"completed\"]}/{results[\"total\"]}')
    for exp in results['experiments']:
        print(f'  {exp[\"name\"]}: {exp[\"status\"]}')
"
```

---

## ⚠️ 트러블슈팅

### Q1: 첫 실험이 실패하면?

```bash
# 1) 로그 확인
tail -100 logs/runs/*/train.log

# 2) 단일 실험 테스트
python scripts/train.py model.hidden_dim=256 training.epochs=10

# 3) 빠른 테스트
python scripts/quick_test.py
```

### Q2: 중간에 멈추고 싶으면?

```bash
# Ctrl+C 누르기
# → 현재 실험 중단
# → 프롬프트에서 다음 실험 계속 여부 선택 가능

# 백그라운드 프로세스 강제 종료
pkill -f "run_experiments"
```

### Q3: 메모리 부족?

```bash
# 모델 크기 줄이기
python scripts/run_experiments.py  # 수정 후

# scripts/run_experiments.py의 hidden_dim 값 수정:
# "model.hidden_dim": 128,  (기본: 256)
```

### Q4: W&B 연결 실패?

```bash
# W&B 로그인 확인
wandb login

# 또는 W&B 비활성화하고 로컬에서만 실행
training.use_wandb=false
```

---

## 💡 팁과 꿀팁

### 1️⃣ 최적의 실행 시간
- **작업 중**: `python scripts/quick_test.py` (5분)
- **점심시간**: 빠른 비교 실험 (~30분)
- **퇴근 후**: `python scripts/run_experiments.py` (3시간)

### 2️⃣ 병렬 실행
```bash
# 터미널 1에서
python scripts/run_experiments.py

# 터미널 2에서 다른 작업
python scripts/train.py model.hidden_dim=768 training.epochs=100
```

### 3️⃣ W&B 설정 최적화
```bash
# 처음 사용 시 한 번만
wandb login

# 로컬 전용으로 실행 (W&B 업로드 없음)
training.use_wandb=false
```

### 4️⃣ 실험 결과 비교
```bash
# W&B에서 직접 비교
1. https://wandb.ai/reports
2. 5개 run 모두 선택
3. 그래프 비교 & 메트릭 분석
```

---

## 📚 관련 문서

| 문서 | 내용 |
|-----|------|
| [`EXPERIMENTS_GUIDE.md`](EXPERIMENTS_GUIDE.md) | 📖 전체 상세 가이드 |
| [`QUICK_START_EXPERIMENTS.md`](QUICK_START_EXPERIMENTS.md) | ⚡ 빠른 참조 & FAQ |
| [`EXPERIMENTS_SUMMARY.md`](EXPERIMENTS_SUMMARY.md) | 📊 기술 상세 정보 |
| `README.md` | 🎯 프로젝트 메인 가이드 |

---

## 🎓 학습 포인트

이 실험 시스템을 통해:

1. **모델 아키텍처의 영향** 평가
   - 깊이 (Depth): 02_DeepModel vs 01_Baseline
   - 폭 (Width): 03_WideModel vs 01_Baseline  
   - 잠재 차원: 04_LargeLatent vs 01_Baseline

2. **조기 종료의 효과** 확인
   - 수렴 속도 개선
   - 과적합 방지
   - 계산 자원 효율화

3. **하이퍼파라미터 최적화** 실습
   - 학습률 스케줄
   - 배치 크기의 영향
   - 조기 종료 파라미터

4. **자동 실험 관리** 경험
   - 스크립트 기반 자동화
   - 결과 추적 및 저장
   - 대규모 실험 관리

---

## ✨ 핵심 특징 정리

| 특징 | 설명 |
|-----|------|
| 🤖 **자동화** | 한 번의 명령으로 5개 실험 순차 실행 |
| ⏱️ **효율성** | 조기 종료로 70% 시간 단축 |
| 📊 **추적성** | W&B + 로컬 로그로 완전 기록 |
| 🔍 **투명성** | 모든 설정 및 결과 가시화 |
| 🛠️ **확장성** | 쉽게 실험 추가/수정 가능 |
| 📚 **문서화** | 상세 가이드 & 빠른 참조 제공 |

---

## 🚀 지금 시작하기

### 방법 1: 즉시 실행
```bash
cd /Users/neo/ACCEL/WienerMachine/Benchmark/FitnessTracker
python scripts/run_experiments.py
```

### 방법 2: 백그라운드에서 실행
```bash
nohup python scripts/run_experiments.py > logs/experiments.log 2>&1 &
```

### 방법 3: 빠른 테스트
```bash
python scripts/quick_test.py
```

---

**🎉 모든 준비가 완료되었습니다!**

더 자세한 정보는 [`EXPERIMENTS_GUIDE.md`](EXPERIMENTS_GUIDE.md) 를 참조하세요.

**행운을 빕니다! 🚀**
