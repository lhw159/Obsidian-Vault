RawImageConverter(x-ray이미지 처리)-> markerPoseGenerator (내일 확인)

shapeinitial파일 results/s0##/left or right/ 에 넣기

GUI_BPF_GMP_Main->Initial Pose Estimation(InitialPoseEstimation 프로그램 활성화)
=> 
SetStartEnd_PeakDetect 프레임 설정(보행 시작-끝, heel strike-toe off)
5프레임 마다 뼈 맞추기 (y 누르면서 (이전 프레임 동기화))
다 맞춘 후 smoothing
per frame 6dof Optimization
->

calibration폴더 내 InitialTxf, InitialTxf_Smoothed 생성
Txfmats/fem or tib 폴더 내 결과 파일