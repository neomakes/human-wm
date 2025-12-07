# 🔬 순차 실험 실행 완전 가이드

> **작성일**: 2025-12-07  
> **상태**: ✅ 완료 및 테스트됨  
> **목표**: 조기 종료 기능과 통합된 5개 실험을 자동으로 순차 실행

---

## 📌 한눈에 보기

### ✨ 새로운 기능
조기 종료 기능과 통합되어, **여러 실험을 순차적으로 자동 실행**할 수 있습니다.

### 🎯 핵심 목표
5개의 서로 다른 모델 설정으로 **모델 아키텍처의 영향을 체계적으로 평가**합니다.

### ⏱️ 소요 시간
약 **3시간** (조기 종료 적용, 전체 200 에포크 설정)

---

## 📋 개요

`run_experiments.py`를 사용하면 여러 실험을 **순차적으로 자동 실행**할 수 있습니다.

각 실험이 **조기 종료(Early Stopping)**되면 자동으로 다음 실험이 시작되므로, 한 번의 명령으로 전체 실험 시리즈를 실행할 수 있습니다.

---

## 🚀 빠른 시작

### 1. 기본 실행

```bash
cd /Users/neo/ACCEL/WienerMachine/Benchmark/FitnessTracker

# Python 스크립트 직접 실행
python scripts/run_experiments.py

# 또는 셸 스크립트 사용
bash scripts/run_all_experiments.sh
```

### 2. 백그라운드에서 실행

```bash
# nohup 사용 (터미널 종료 후에도 계속 실행)
nohup python scripts/run_experiments.py > logs/experiments.log 2>&1 &

# 또는 tmux 사용
tmux new-session -d -s experiments "python scripts/run_experiments.py"
tmux attach -t experiments
```

---

## 📊 실험 구성

### Experiment 1: Baseline (기본 설정)
```yaml
모델:
  - Hidden dim: 256
  - Layers: 2
  - Latent dims: z_a=16, z_b=32, z_c=32

훈련:
  - Batch size: 32
  - Learning rate: 0.001
  - Max epochs: 200
  - Early stopping patience: 10
```

### Experiment 2: Deep Model (더 깊은 모델)
```yaml
모델:
  - Hidden dim: 256
  - Layers: 3  ← 깊어짐
  - Latent dims: z_a=16, z_b=32, z_c=32

목표: 모델 깊이의 영향 평가
```

### Experiment 3: Wide Model (더 넓은 모델)
```yaml
모델:
  - Hidden dim: 512  ← 넓어짐
  - Layers: 2
  - Latent dims: z_a=16, z_b=32, z_c=32

목표: 모델 폭의 영향 평가
```

### Experiment 4: Large Latent (큰 잠재 차원)
```yaml
모델:
  - Hidden dim: 256
  - Layers: 2
  - Latent dims: z_a=32, z_b=64, z_c=64  ← 더 큼

목표: 잠재 차원 크기의 영향 평가
```

### Experiment 5: Optimized (최적화된 설정)
```yaml
모델:
  - Hidden dim: 512
  - Layers: 2
  - Latent dims: z_a=32, z_b=64, z_c=64

훈련:
  - Learning rate: 0.0005  ← 더 낮음
  
목표: 모든 최적화를 적용한 최고 성능 모델
```

---

## 📈 실행 흐름

```
시작
  ↓
Experiment 1 실행
  ↓ (조기 종료)
Experiment 2 실행
  ↓ (조기 종료)
Experiment 3 실행
  ↓ (조기 종료)
Experiment 4 실행
  ↓ (조기 종료)
Experiment 5 실행
  ↓ (조기 종료)
최종 결과 생성 & 저장
```

**각 실험은 완전히 독립적으로** 실행되므로, 이전 실험의 결과에 영향을 받지 않습니다.

---

## 🔧 커스터마이징

### 실험 추가하기

`scripts/run_experiments.py`의 `EXPERIMENTS` 리스트를 수정:

```python
EXPERIMENTS = [
    {
        "name": "06_CustomExperiment",
        "description": "커스텀 설정",
        "params": {
            "model.hidden_dim": 128,
            "model.num_layers": 4,
            "training.learning_rate": 0.0001,
            # ... 다른 파라미터들
        }
    },
    # ... 기존 실험들
]
```

### 특정 실험만 실행하기

Python에서 직접 호출:

```python
from scripts.run_experiments import run_experiment, EXPERIMENTS

# Experiment 2만 실행
run_experiment(EXPERIMENTS[1], 1, len(EXPERIMENTS))
```

또는 CLI에서:

```bash
# 단일 실험 실행 (기존 방식)
cd /Users/neo/ACCEL/WienerMachine/Benchmark/FitnessTracker
python scripts/train.py \
  model.hidden_dim=512 \
  model.num_layers=3 \
  training.learning_rate=0.0005
```

---

## 📊 결과 확인

### 1. 실시간 모니터링

#### W&B 대시보드
```
https://wandb.ai/your-username/fitness-tracker
```

각 실험이 별도의 run으로 기록되므로, 대시보드에서 비교 가능합니다.

#### 로그 파일
```bash
# 가장 최근 실험 로그 확인
tail -f logs/runs/*/train.log

# 모든 실험 결과 요약
cat logs/experiments_results.json
```

### 2. 최종 결과 분석

```bash
python -c "
import json
with open('logs/experiments_results.json') as f:
    results = json.load(f)
    print(json.dumps(results, indent=2))
"
```

결과 파일 구조:
```json
{
  "start_time": "2025-12-07T10:00:00",
  "total": 5,
  "completed": 5,
  "experiments": [
    {"name": "01_Baseline", "status": "completed"},
    {"name": "02_DeepModel", "status": "completed"},
    ...
  ]
}
```

---

## ⚠️ 주의사항

### 1. 조기 종료 설정
- `early_stopping_patience=10`: 10 epoch 동안 개선이 없으면 멈춤
- 실험 시간을 크게 단축할 수 있습니다

### 2. W&B 프로젝트 설정
실험을 시작하기 전에 W&B 로그인 확인:
```bash
wandb login
```

### 3. 중단 및 재개
- **Ctrl+C**: 현재 실험 중단
- 다시 실행하면 **다음 실험부터 시작**

### 4. 리소스 관리
- 각 실험은 독립적인 모델을 생성하므로 메모리 사용량이 누적됩니다
- 메모리 부족 시 `model.hidden_dim` 값을 줄이세요

---

## 🎯 권장 활용

### 시나리오 1: 최적의 설정 찾기
```bash
# 밤새 실행
nohup python scripts/run_experiments.py > logs/experiments.log 2>&1 &

# 아침에 결과 확인
cat logs/experiments_results.json
```

### 시나리오 2: 특정 설정 비교
`EXPERIMENTS` 리스트의 일부만 선택해서 수정 후 실행

### 시나리오 3: 하이퍼파라미터 스윕
여러 버전의 `run_experiments.py`를 만들어 각각 다른 설정으로 실행

---

## 🐛 트러블슈팅

### Q1: 모든 실험이 조기 종료되지 않음

**확인사항:**
```bash
# 조기 종료 설정 확인
grep early_stopping conf/training/default.yaml
```

```yaml
early_stopping_patience: 5
early_stopping_min_delta: 0.001
```

### Q2: 특정 실험에서 에러 발생

로그 파일 확인:
```bash
# 가장 최근 실험의 로그
tail -100 logs/runs/*/train.log
```

### Q3: W&B 업로드 느림

네트워크 문제일 수 있음. 로컬 테스트 후 W&B 활성화:
```bash
training.use_wandb=false  # 먼저 로컬에서만 테스트
```

---

## 📚 관련 파일

- `scripts/run_experiments.py`: 메인 실험 스크립트
- `scripts/run_all_experiments.sh`: 셸 래퍼
- `scripts/train.py`: 개별 실험 실행 스크립트
- `conf/training/default.yaml`: 훈련 설정
- `logs/experiments_results.json`: 최종 결과 저장

---

## 🎓 학습 포인트

이 설정을 통해 다음을 학습할 수 있습니다:

1. **모델 아키텍처의 영향**: Deep vs Wide vs Large Latent
2. **조기 종료의 효과**: 수렴 속도와 최종 성능
3. **학습률 스케줄링**: 0.001 vs 0.0005
4. **자동 실험 관리**: 대규모 하이퍼파라미터 스윕

---

# 📖 추가 섹션: 실행 방법 & 결과 분석

## 🚀 3가지 실행 방법

### 방법 1: Python 스크립트 직접 실행
```bash
cd /Users/neo/ACCEL/WienerMachine/Benchmark/FitnessTracker
python scripts/run_experiments.py
```

### 방법 2: 셸 스크립트 (권장)
```bash
bash scripts/run_all_experiments.sh
```

### 방법 3: 백그라운드 실행 (밤새 실행하고 싶을 때)

#### nohup 사용
```bash
nohup python scripts/run_experiments.py > logs/experiments.log 2>&1 &

# 진행 상황 확인
tail -f logs/experiments.log

# 상태 확인
ps aux | grep run_experiments
```

#### tmux 사용
```bash
# 새로운 tmux 세션 생성
tmux new-session -d -s exp "python scripts/run_experiments.py"

# 세션에 접속
tmux attach -t exp

# 세션 목록 확인
tmux list-sessions

# 세션에서 빠져나오기 (세션은 계속 실행)
Ctrl+B D
```

---

## ✨ 각 실험의 예상 소요 시간

| 실험 | 모델 크기 | 예상 시간 |
|------|---------|---------|
| 01 Baseline | 256-dim, 2L | ~30분 |
| 02 Deep Model | 256-dim, 3L | ~35분 |
| 03 Wide Model | 512-dim, 2L | ~40분 |
| 04 Large Latent | 256-dim, 64-lat | ~35분 |
| 05 Optimized | 512-dim, 64-lat | ~45분 |
| **총합** | | **~185분 (3시간)** |

*조기 종료 미적용 시 각 ~60분 → 총 ~300분

---

## 🔧 빠른 테스트 (개발/디버깅용)

```bash
# 에포크 20, W&B 미사용 (매우 빠름)
python scripts/quick_test.py
```

**목적**: 
- 코드 변경 후 빠르게 테스트
- 모든 설정 정상 작동 확인
- 메모리/GPU 문제 확인

---

## 📈 결과 확인

### 1. 실시간 로그 보기
```bash
# 가장 최근 로그 실시간 확인
tail -f logs/runs/*/train.log

# 모든 로그 한 줄씩 표시
grep "Epoch" logs/runs/*/train.log | tail -20
```

### 2. W&B 대시보드
```
https://wandb.ai/your-username/fitness-tracker
```
- 각 실험이 별도 run으로 기록됨
- 실시간 메트릭 시각화
- 실험 간 비교 가능

### 3. 최종 결과 파일
```bash
# JSON 형식 결과 보기
cat logs/experiments_results.json | python -m json.tool

# 또는
python -c "
import json
with open('logs/experiments_results.json') as f:
    results = json.load(f)
    for exp in results['experiments']:
        print(f\"{exp['name']}: {exp['status']}\")
"
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

## 🎯 권장 사용 시나리오

### 시나리오 1: 최고 성능 모델 찾기
```bash
# 밤 10시에 시작
nohup python scripts/run_experiments.py > logs/experiments.log 2>&1 &

# 아침 1시경 완료
# 결과 분석 후 최적 모델 선정
```

### 시나리오 2: 빠른 비교 실험
```bash
# 에포크 50으로 줄여서 ~1시간 내에 완료
python scripts/quick_test.py  # 또는 EXPERIMENTS 수정

# 빠르게 상위 N개 설정 선정
```

### 시나리오 3: 특정 설정 깊이 있게 테스트
```bash
# 특정 실험만 에포크 500으로 실행
python scripts/train.py \
  model.hidden_dim=512 \
  model.num_layers=2 \
  model.latent_state_dim=32 \
  training.epochs=500 \
  training.early_stopping_patience=15 \
  training.use_wandb=true
```

---

## 📋 생성된 파일 목록

### 🔧 스크립트 (scripts/)
```
├─ run_experiments.py        (8.7KB) - 메인 실험 스크립트 ⭐
├─ quick_test.py             (3.2KB) - 빠른 테스트용 (에포크 20)
└─ run_all_experiments.sh     (실행권한 설정 ✓)
```

### ⚙️ 설정 변경 (conf/)
```
└─ training/default.yaml
   ├─ early_stopping_patience: 5
   └─ early_stopping_min_delta: 0.001
```

---

## 🔬 자동 실행되는 5개 실험

| # | 이름 | 설정 | 목표 |
|---|------|------|------|
| 1️⃣ | **Baseline** | 256-dim, 2L, latent=16/32/32 | 기본 성능 기준 |
| 2️⃣ | **Deep Model** | 256-dim, **3L**, latent=16/32/32 | 깊이의 영향 평가 |
| 3️⃣ | **Wide Model** | **512-dim**, 2L, latent=16/32/32 | 폭의 영향 평가 |
| 4️⃣ | **Large Latent** | 256-dim, 2L, **latent=32/64/64** | 잠재 차원의 영향 |
| 5️⃣ | **Optimized** | **512-dim, 2L, latent=32/64/64, lr=0.0005** | 최고 성능 추구 |

---

## 📊 실행 흐름도

```
시작
  ↓
[01] Baseline (최대 200 에포크, 조기 종료시 중단)
  ↓ (조기 종료)
[02] Deep Model
  ↓ (조기 종료)
[03] Wide Model
  ↓ (조기 종료)
[04] Large Latent
  ↓ (조기 종료)
[05] Optimized
  ↓ (조기 종료)
📊 최종 결과 생성 (logs/experiments_results.json)
  ↓
✅ 완료!
```

---

## 💾 파일 구조 (완전)

```
scripts/
├─ train.py                    (기존, 개선됨)
│  └─ EarlyStoppingTracker 클래스 추가
│
├─ run_experiments.py          (NEW)
│  └─ 5개 실험 순차 실행
│
├─ quick_test.py              (NEW)
│  └─ 빠른 테스트용 (에포크 20)
│
└─ run_all_experiments.sh      (NEW)
   └─ 셸 래퍼

conf/
└─ training/
   └─ default.yaml            (수정)
      └─ early_stopping_patience: 5
         early_stopping_min_delta: 0.001

docs/
├─ [03] ExperimentGuide.md     ← 이 문서 (통합 가이드)
├─ [02] ModelDesign.md         (기존)
└─ ...

logs/
└─ experiments_results.json    (NEW, 실행 후 생성)
   └─ 최종 결과 통계
```

---

## 📈 실행 예상 시간

```
조기 종료 적용 시 (5 epoch 패턴):
├─ 01 Baseline:        ~30분 (평균 60개 epoch)
├─ 02 DeepModel:       ~35분
├─ 03 WideModel:       ~40분
├─ 04 LargeLatent:      ~35분
└─ 05 Optimized:       ~45분
━━━━━━━━━━━━━━━━━━━━━━
총합: ~185분 (3시간) ⚡

vs 조기 종료 미적용 (모든 200 epoch):
총합: ~600분 (10시간)

효율성: 약 3배 시간 단축! 🚀
```

---

## ✨ 핵심 장점

| 장점 | 설명 |
|-----|------|
| 🤖 **자동화** | 한 번의 명령으로 5개 실험 모두 실행 |
| ⏱️ **시간 절약** | 조기 종료로 3시간 내 완료 |
| 📊 **체계적** | 실험 결과 자동 수집 및 저장 |
| 🔍 **추적성** | W&B와 로컬 로그로 완전 추적 |
| 🛠️ **확장성** | 쉽게 실험 추가/수정 가능 |
| 📚 **문서화** | 이 완전 가이드로 모든 정보 제공 |

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

# 🛡️ macOS Sleep 모드 방지 (caffeinate)

## 개요

macOS의 `caffeinate` 명령어를 사용하여 **학습 중 절전 모드를 자동으로 방지**합니다.
**프로세스가 완료되면 자동으로 절전 모드가 복구됩니다!**

---

## 🚀 사용 방법

### 방법 1: Makefile (가장 간단, 권장) ⭐

```bash
# 단일 학습
make train-safe

# 순차 실험
make experiments-safe

# 빠른 테스트
make quick-test
```

### 방법 2: 직접 명령어

```bash
# 단일 학습
caffeinate -i python scripts/train.py training.epochs=100

# 순차 실험
caffeinate -i python scripts/run_experiments.py

# 또는 셸 스크립트
bash scripts/run_experiments_safe.sh
```

### 방법 3: 백그라운드 + tmux (네트워크 끊김 방지)

```bash
# tmux 세션에서 실행
tmux new-session -d -s fitness-exp \
  'cd ~/ACCEL/WienerMachine/Benchmark/FitnessTracker && make experiments-safe'

# 진행 상황 확인
tmux attach-session -t fitness-exp

# 백그라운드에서 계속 실행
Ctrl+B D
```

---

## ⚙️ caffeinate 옵션 설명

| 옵션 | 효과 | 사용 시기 |
|-----|------|---------|
| `-i` | 유휴 상태에서만 잠자기 방지 | **권장** (안정적) |
| `-m` | 디스플레이 잠자기만 방지 | 디스플레이만 중요할 때 |
| `-s` | 시스템 전체 잠자기 방지 | 매우 중요한 작업 |

**기본 설정**: `caffeinate -i`
- 키보드/마우스 활동이 없을 때만 작동
- 사용자가 작업 중이면 시스템 정상 작동
- 가장 안정적이고 자연스러움

---

## 🛠️ 생성된 파일 & 수정사항

### 새로 생성된 파일

1. **scripts/run_experiments_safe.sh**
   - caffeinate로 순차 실험 실행
   - 자동 로깅 및 절전 모드 복구

2. **Makefile**
   - `make train-safe`: 단일 학습 (sleep 방지)
   - `make experiments-safe`: 순차 실험 (sleep 방지)
   - 기타 유틸리티 명령어

### 수정된 파일

1. **scripts/train.py**
   - `check_caffeinate()` 함수 추가
   - macOS에서 caffeinate 감지 및 로깅

2. **scripts/run_experiments.py**
   - `check_caffeinate()` 함수 추가
   - 실험 시작 시 caffeinate 상태 확인

---

## 🔍 작동 확인

### caffeinate 활성화 시 (정상)
```
✅ caffeinate 감지됨 - Sleep 모드가 방지되고 있습니다
💡 프로세스 완료 후 절전 모드가 자동으로 복구됩니다
```

### caffeinate 미사용 시 (경고)
```
⚠️  caffeinate 없이 실행 중입니다
💡 권장: make train-safe 또는 make experiments-safe
```

---

## 💡 Makefile 명령어

### Sleep 모드 방지 (권장)
```bash
make train-safe          → 단일 학습 + sleep 방지
make experiments-safe    → 순차 실험 + sleep 방지
make t-safe              → 단축 명령 (train-safe)
make exp-safe            → 단축 명령 (experiments-safe)
```

### 기본 실행 (sleep 방지 없음)
```bash
make train               → 단일 학습
make experiments         → 순차 실험
make quick-test          → 빠른 테스트 (에포크 20)
make q                   → 단축 명령 (quick-test)
```

### 유틸리티
```bash
make help                → 전체 명령어
make help-safe           → Sleep 모드 옵션 설명
make logs                → 최근 로그 확인
make clean               → 캐시 정리
```

---

## 🎯 권장 시나리오

### 시나리오 1: 빠른 테스트 (30분)
```bash
make quick-test
```

### 시나리오 2: 단일 학습 (1시간)
```bash
make train-safe
```

### 시나리오 3: 순차 실험 - 밤새 (3시간)
```bash
make experiments-safe
```

### 시나리오 4: 백그라운드 실행 (가장 안전)
```bash
nohup make experiments-safe > logs/exp_safe.log 2>&1 &
tail -f logs/exp_safe.log  # 진행 상황 모니터링
```

### 시나리오 5: tmux 세션 (네트워크 끊김 방지)
```bash
tmux new-session -d -s fitness 'make experiments-safe'
tmux attach -t fitness
```

---

## ❓ FAQ

**Q1: 프로세스가 끝나면 정말 절전 모드가 복구되나요?**

A: 네, 맞습니다! `caffeinate`는 프로세스가 종료되면 **자동으로 절전 모드 설정을 원래 상태로 복구**합니다.

**Q2: 학습 중 Mac을 사용해도 되나요?**

A: 네, `-i` 옵션을 사용하면 **유휴 상태**를 감지합니다:
- 키보드/마우스로 작업 중 → 절전 모드 방지 안 함 (정상 작동)
- 유휴 상태 (아무 활동 없음) → 절전 모드 방지 (학습 보호)

**Q3: 다른 옵션 (예: `-s`)을 사용할 수 있나요?**

A: 네, 필요하면 직접 명령어를 사용할 수 있습니다:
```bash
caffeinate -is python scripts/run_experiments.py
```

**Q4: caffeinate는 어디서 설치하나요?**

A: macOS 기본 내장 명령어입니다! 추가 설치 필요 없습니다.
```bash
which caffeinate
man caffeinate  # 매뉴얼 보기
```

**Q5: 여러 개 프로세스를 동시에 caffeinate로 실행할 수 있나요?**

A: 네, 가능합니다:
```bash
# 터미널 1
caffeinate -i python scripts/train.py model.hidden_dim=256

# 터미널 2 (다른 터미널에서)
caffeinate -i python scripts/train.py model.hidden_dim=512
```

---

## 📊 예상 소요 시간

```
조기 종료 적용 시:
├─ 01 Baseline:        ~30분
├─ 02 DeepModel:       ~35분
├─ 03 WideModel:       ~40분
├─ 04 LargeLatent:      ~35분
└─ 05 Optimized:       ~45분
━━━━━━━━━━━━━━━━━━━━━━
총합: ~185분 (3시간) ⚡

Sleep 모드 자동 방지 + 자동 복구 ✅
```

---

**🎉 모든 준비가 완료되었습니다!**

지금 바로 시작하세요:
```bash
cd /Users/neo/ACCEL/WienerMachine/Benchmark/FitnessTracker
make experiments-safe
```

더 자세한 정보는 소스 코드의 주석을 참조하세요:
- `scripts/run_experiments.py`: 메인 스크립트의 상세 주석
- `scripts/train.py`: EarlyStoppingTracker 클래스 구현

**행운을 빕니다! 🚀**
