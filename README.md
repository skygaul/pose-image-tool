# 원하는 포즈로 이미지 만드는 도구

## 도구 설명
- 참조 사진에서 **OpenPose** 로 관절(뼈대)을 뽑고, 그 뼈대를 **ControlNet** 조건으로 넣어 **같은 자세의 다른 인물·장면** 을 만듭니다.
- 출발점은 교안의 FLUX.2-klein-4B (GGUF Q4) + ComfyUI Colab 노트이고, 여기에 포즈 조건을 얹었습니다.
- FLUX.2-klein 에는 공식 포즈 ControlNet 이 없어서, 포즈 생성은 무료 T4 에서 안정적으로 도는 **SD 1.5 + ControlNet OpenPose** 로 하고, klein 은 비교 기준(포즈 조건 없음, 참조 이미지 방식)으로 썼습니다.
- **개선판 도구(10-E)** 는 어떤 사진이든 잘 나오도록 다음을 자동으로 합니다.
  - 거꾸로 선 사진 판별(0도·180도 비교)
  - 사람 영역 크롭과 1024 해상도 뼈대 추출
  - 전신 구도 강제
  - 후보 여러 장 생성 후 **자세 일치도** 가 가장 높은 결과 선택

## 사용법
1. `pose_tool.ipynb` 를 Colab 에서 엽니다. (GitHub 에서 파일을 연 뒤 주소의 `github.com` 을 `colab.research.google.com/github` 로 바꾸면 바로 열립니다.)
2. **[런타임 → 런타임 유형 변경 → T4 GPU]** 를 고릅니다.
3. 셀을 위에서부터 순서대로 실행합니다. 설치, 모델 다운로드, 서버 실행(1~6), 포즈 추출(7), 생성(8) 순서입니다.
4. 사진 한 장으로 바로 쓰려면 10-E 셀까지 실행한 뒤 아래 한 줄을 실행합니다.
   `pose_tool_v2(upload_photo("ref_03.jpg"), "프롬프트", "v2_pose_03")`
5. 웹 화면으로 쓰려면 **11번 셀** 을 실행하고 `gradio.live` 링크를 엽니다.
6. 결과는 `samples/` 에 저장되고, 12번 셀에서 zip 으로 내려받습니다.

## 테스트 결과
| 포즈 | 프롬프트 | 결과 |
|---|---|---|
| 포즈 1 (`pose_01`): 바닥에 앉아 뒤로 기대고 한쪽 무릎을 세운 자세 | a female astronaut in a white spacesuit standing on the moon ... | `output_01.png`: 앉아서 기대고 무릎을 세운 큰 자세는 맞았지만, 무릎 위에 올린 팔이 어색했고 한쪽 다리가 떠 보였습니다. |
| 포즈 1, 개선판 | (같은 프롬프트) | `v2_pose_01_best.png`: 무릎 위 팔과 손까지 뼈대에 잡혀 팔 자세가 참조 사진과 같아졌습니다. 자세 일치도 0.50 (후보 0.35~0.50). |
| 포즈 2 (`pose_02`): 브레이크댄스 헤드스핀 | (같은 프롬프트) | `output_02.png`: **개선 전 실패 사례.** OpenPose 가 거꾸로 선 사람을 똑바로 선 사람으로 읽어, 팔을 벌리고 선 우주비행사가 나왔습니다. |
| 포즈 3 (`pose_03`): 다리를 꼰 헤드스핀, 개선판 UI | an astronaut ... doing an upside-down breakdance headstand freeze on the moon, head on the ground, legs in the air | `output_03.png`: 180도 방향을 자동으로 판별해 **머리가 아래·다리가 위인 거꾸로 선 자세** 가 나왔습니다. 자세 일치도 0.27. |

- 같은 포즈에 프롬프트만 바꾸면(기사, 만화 고양이, 댄서) 사람 대상은 자세를 따랐지만 **고양이는 자세를 거의 무시** 했습니다.
- ControlNet 강도는 **0.3 에서 자세가 무너지고, 0.6 이 가장 자연스럽고, 1.0 이 가장 정확** 했습니다.
- 자세한 관찰은 노트 마지막 셀의 **실험 기록** 에 있습니다.

## 한계
- **가려지거나 겹친 관절**(다리와 겹친 팔, 꼰 다리)은 뼈대가 앞뒤를 구분하지 못해 틀리기 쉽습니다.
- **거꾸로 선 자세** 는 방향 판별로 추출은 해결했지만, SD 1.5 가 거꾸로 선 사람을 잘 그리지 못해 후보에 따라 몸통과 팔이 뒤엉켰습니다.
- **사람이 아닌 대상**(동물, 캐릭터)은 사람 뼈대를 따르지 않습니다.
- 뼈대에는 바닥 정보가 없어서 앉은 인물이 **떠 보이는** 경우가 있습니다.
- 다음 단계로 SDXL + ControlNet OpenPose, 또는 Depth ControlNet 을 함께 쓰는 방법을 시도해 볼 수 있습니다.

## 폴더 구성
| 파일 | 내용 |
|---|---|
| `pose_tool.ipynb` | Colab 튜토리얼 노트 (모델 로드 → 포즈 추출 → 생성 → 실험 → 개선판 → UI → 저장) |
| `samples/ref_0N.jpg` | 참조로 쓴 원본 사진 |
| `samples/pose_0N.png` | 원본 사진에서 뽑은 뼈대 그림 |
| `samples/output_0N.png` | 같은 자세로 만든 결과 |
| `samples/baseline_*.png`, `exp_*.png` | 조건 실험 결과 (0, A, C, D) |
| `samples/v2_*.png`, `ui_*.png` | 개선판 도구 결과와 후보 |
| `prompts.md` | 테스트에 쓴 프롬프트 모음 |

## 사용한 모델과 라이선스
| 구성 요소 | 라이선스 |
|---|---|
| FLUX.2-klein-4B (unsloth GGUF Q4) | Apache 2.0 |
| Stable Diffusion 1.5 | CreativeML OpenRAIL-M |
| ControlNet v1.1 OpenPose (fp16) | OpenRAIL |
| OpenPose 포즈 추출 모델 (controlnet_aux) | CMU OpenPose 계열, 비상업·학술용 (교육 목적으로 사용) |

## 참조 사진 출처
모두 Pexels 무료 라이선스 사진입니다.
- 포즈 1 (앉은 자세): Photo by Gabriel Machado from Pexels — https://www.pexels.com/ko-kr/photo/35452275/
- 포즈 2·3 (헤드스핀): Photo by Allan Mas from Pexels — https://www.pexels.com/photo/flexible-asian-man-doing-handstand-on-street-5370388/
