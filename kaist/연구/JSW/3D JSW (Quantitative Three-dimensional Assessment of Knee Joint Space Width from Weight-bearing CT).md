**Quantitative Three-dimensional Assessment of Knee Joint Space Width from Weight-bearing CT**

Tom D. Turmezei, MPhil, MA, BM BCh, PhD, FRCR • Samantha B. Low, FBEng, MBBS, FRCR • Simon Rupret, MD • Graham M. Treece, MA, PhD • Andrew H. Gee, MA, PhD • James W. MacKay, MA, MB BChir, PhD, FRCR • John A. Lynch, PhD • Kenneth E. S. Poole, BM, PhD, MRCP • Neil A. Segal, MS, MD



## mesh 방식 타당성 
완전히 타당한 지적이고, 실제로 그렇게 됩니다 — 논문도 이 문제를 알아서 두 군데서 막아놓은 걸 제가 놓치고 설명을 덜 했습니다.

말씀하신 문제가 실제로 일어나는 게 맞습니다: 법선이 살짝 틀어진 정점은 ray가 반대쪽 뼈를 못 맞히거나 비스듬히 맞혀서 그 점만 값이 튀거나 실패합니다. 표면이 거칠수록(=법선이 들쭉날쭉할수록) 점마다 이게 무작위로 발생해서 말씀하신 "중구난방" 현상이 그대로 나옵니다. 이건 제가 전에 "법선은 위치의 미분이라 노이즈가 증폭된다"고 한 것의 직접적 결과입니다.

**논문이 이걸 막는 방법은 두 군데 있습니다:**

1. 입력 자체를 훨씬 매끈하게 만듭니다: Fig 1A를 보면 "shape model–assisted segmentation... with 1.5mm between segmented axial sections"라고 되어 있습니다. 저희처럼 CT 전체 slice(0.3~0.6mm 간격)를 다 마칭큐브로 뼈-공기 경계 그대로 뜨는 게 아니라, 1.5mm마다 사람이(또는 반자동으로) 부드러운 contour(스플라인)를 그리고 그 사이를 보간해서 표면을 만듭니다. 이 자체가 이미 저희 raw marching-cubes STL보다 훨씬 매끈한 시작점입니다 — 저희가 논의했던 "Taubin smoothing 몇 번 돌리기"보다 훨씬 강한 수준의 평활화가 세그멘테이션 단계에서부터 들어가 있는 겁니다.

2. 결과 자체도 또 한 번 블러 처리합니다: Fig 1I 캡션에 "Independent measurements are repeated across all vertices in the patch and blurred across the surface"라고 명시되어 있고, canonical surface로 옮길 때도 "with some blurring of data before and after transfer"라고 되어 있습니다. 즉 점 하나하나의 값을 그대로 믿는 게 아니라, 측정 후에도 공간적으로 다시 평균/블러해서 개별 점의 튐을 지웁니다.

정리하면: 논문 방법은 "법선으로 재면 끝"이 아니라 **(매끈한 입력) + (개별측정) + (결과 블러)**의 3단계로, 말씀하신 문제를 두 번(입력·출력) 완충해야 겨우 쓸만해지는 구조입니다. 반면 저희 patch3mm 방식은 SI축이라는 애초에 흔들리지 않는 방향을 쓰고, nan_boxmean으로 한 번만 공간평균해도 됩니다 — 같은 문제를 훨씬 적은 안전장치로 해결하는 셈입니다. 그러니까 이건 "저희 방법이 덜 정교해서 단순화한 것"이 아니라, 애초에 그 실패모드 자체가 구조적으로 덜 발생하도록 설계된 것에 가깝습니다.

## 경계 라인 기준
네, 있습니다 — 오히려 저희 방식을 상당히 직접적으로 뒷받침하는 내용입니다.

1) 경계(perimeter) 자체도 사람이 수동으로 그립니다 (Fig 1F: "Manual markup of this perimeter for extraction of the tibiofemoral joint space patch") — 자동 shadow 기법으로 후보를 잡고, 최종 확정은 사람이 합니다. 저희가 육안으로 eminence/margin 경계를 정하기로 한 것과 같은 종류의 선택입니다.

2) 가장자리(margin)가 덜 신뢰할만하다는 걸 논문이 직접 인정합니다 — Discussion에 이렇게 나옵니다.
	"this technique does show some drop-off in performance toward the margins of the joint space, a result of the human operator perimeter definition step that we are now looking to automate"

즉 관절면 가장자리로 갈수록 재현성이 떨어지고, 그 이유가 "경계를 어디로 그을지"의 불확실성(사람마다 조금씩 다르게 그음) 때문이라고 스스로 밝히고 있습니다. 이건 저희가 roi_frac으로 바깥쪽 변연부를 잘라내는 것과 똑같은 문제의식입니다.

3) 실제로 중앙부가 가장 재현성이 좋다는 수치까지 있습니다 — Table 2 "Best LOA (mm)" 항목:
	0.16mm (central medial) — reproducibility, KLG<2
	0.06mm (central medial) — reproducibility, KLG=2

전체 patch 평균 LOA(0.40.66mm)보다 중앙부만 따로 보면 훨씬 좋습니다(0.060.16mm) — 정확히 "중앙이 제일 믿을만하다"는 저희 전제와 일치하는 실측 근거입니다. Fig 6에서 유의미한 KLG 관련 변화가 발견된 곳도 "central-to-posterior medial"이라, 임상적으로 의미있는 신호도 중앙부에서 나온다는 것까지 확인됩니다.

정리: "중앙 window만 신뢰하고 바깥/융기부는 잘라낸다"는 저희 설계는 이 논문의 데이터로도 뒷받침됩니다 — 논문도 결국 "가장자리는 덜 믿을만하다"는 같은 결론에 도달했고, 저희는 그걸 사후 분석(그들의 재현성 연구)이 아니라 사전 설계(roi_frac/eminence_hw)로 반영한 차이일 뿐입니다. 인용하실 때 이 Discussion 문장 + Table 2 central LOA 수치를 근거로 쓰시면 될 것 같습니다.