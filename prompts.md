# 테스트에 쓴 프롬프트 모음

## 공통 설정

**기본 도구 (8번 셀, SD 1.5 + ControlNet OpenPose)**
- seed 42, steps 25, cfg 7.0, sampler dpmpp_2m / karras, ControlNet strength 1.0 (실험 C 제외)
- 네거티브:
```
lowres, blurry, bad anatomy, extra limbs, extra fingers, deformed hands, watermark, text
```

**개선판 도구 (10-E 셀, 11번 UI)**
- seeds 42 / 7 / 123 / 2024 (UI 는 시작 시드 42 부터 4개: 42~45), ControlNet strength 0.8
- 프롬프트 앞에 자동으로 붙는 문구:
```
full body shot, wide shot, entire body visible from head to feet,
```
- 네거티브 (기본 네거티브에 추가):
```
deformed arm, twisted arm, extra arms, missing arm, extra legs, bad hands, fused limbs, close-up, portrait, headshot, face only, cropped body
```

## 기본 결과
| 파일 | 포즈 | 프롬프트 |
|---|---|---|
| output_01.png | pose_01 (앉은 자세) | a female astronaut in a white spacesuit standing on the moon, full body, photo, highly detailed |
| output_02.png | pose_02 (헤드스핀) | (위와 같음) |

## 0. 포즈 조건 없음 (FLUX.2-klein-4B, cfg 1.0, 4 steps, seed 42)
| 파일 | 프롬프트 |
|---|---|
| baseline_klein_no_pose.png | a female astronaut in a white spacesuit standing on the moon, full body, photo, highly detailed |

## A. 같은 포즈 · 다른 프롬프트 (pose_01)
| 파일 | 프롬프트 |
|---|---|
| exp_a_1.png | an old knight in silver armor in a castle courtyard, oil painting |
| exp_a_2.png | a cartoon cat character wearing a red hoodie, flat illustration, white background |
| exp_a_3.png | a street dancer in neon lights at night, cinematic photo |

## B. 다른 포즈 · 같은 프롬프트
기본 결과의 `output_01.png` (pose_01) 과 `output_02.png` (pose_02) 를 비교했습니다. 프롬프트는 같습니다.

## C. ControlNet 강도 (pose_01, 기본 프롬프트)
| 파일 | strength |
|---|---|
| exp_c_strength_0.3.png | 0.3 |
| exp_c_strength_0.6.png | 0.6 |
| exp_c_strength_1.0.png | 1.0 |

## D. klein 참조 이미지 방식 (pose_01 을 ReferenceLatent 로 입력)
| 파일 | 프롬프트 |
|---|---|
| exp_d_klein_reference.png | Draw the person in exactly the same pose as the stick figure in the reference image. a female astronaut in a white spacesuit standing on the moon, full body, photo, highly detailed |

## E. 개선판 도구
| 파일 | 참조 사진 | 프롬프트 (앞에 전신 문구 자동 추가) |
|---|---|---|
| v2_pose_01_best.png (후보 v2_pose_01_seed*.png) | 앉은 자세 | a female astronaut in a white spacesuit standing on the moon, full body, photo, highly detailed |
| v2_pose_02_best.png (후보 v2_pose_02_seed*.png) | 헤드스핀 (pose_02) | an astronaut in a white spacesuit doing an upside-down breakdance headstand freeze on the moon, head on the ground, legs in the air |
| output_03.png (= ui_*_best.png), 뼈대 pose_03.png | 다리를 꼰 헤드스핀 (11번 UI) | an astronaut in a white spacesuit doing an upside-down breakdance headstand freeze on the moon, head on the ground, legs in the air |
