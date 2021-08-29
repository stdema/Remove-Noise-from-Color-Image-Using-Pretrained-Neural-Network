# Remove-Noise-from-Color-Image-Using-Pretrained-Neural-Network
- 이 예제에서는 이미지를 별도의 컬러 채널로 분할 후 사전 훈련된 노이즈 제거 신경 네트워크 DnCNN을 사용하여 각 채널에 노이즈를 제거해본다.
노이즈가 있는 사진과 제거된 사진은 각각 원본 과 비교하여 노이즈 제거가 얼마나 잘 되었는지 알 수 있다.

### 이미지 double형 변환 후 디스플레이
```c
pristineRGB = imread('lighthouse.png');  % lighthouse 파일 이미지를 읽음
pristineRGB = im2double(pristineRGB);
 % im2double(I)는 이미지 I를 배정밀도로 변환, 출력값을 정수형에서 [0,1] 스케일링
imshow(pristineRGB)
title('Pristine Image') % 제목(Pristine Image) 추가
```

변환한 이미지 [pristineRGB]

![2021-08-29 (4)](https://user-images.githubusercontent.com/86040099/131248534-13da26cb-8b86-4fa6-9a2d-e952b647e2e1.png)


### 각 색상 채널에 노이즈 추가 후 각 채널 분할
```c
noisyRGB = imnoise(pristineRGB,'gaussian',0,0.01);  % 분산 0.01과 평균 0의 가우스 백색 잡음을 이미지에 추가
imshow(noisyRGB)
title('Noisy Image')

[noisyR,noisyG,noisyB] = imsplit(noisyRGB);     % RGB 이미지 개별 채널로 분할
```

노이즈 추가한 이미지 [noisyRGB]

![2021-08-29 (5)](https://user-images.githubusercontent.com/86040099/131248606-b0444376-0134-4142-b4f8-20de304c31f7.png)

노이즈 추가한 이미지 각 채널 분할 [noisyR,noisyG,noisyB]

![2021-08-29 (6)](https://user-images.githubusercontent.com/86040099/131248644-3a622394-a520-4bcb-a1cd-5688cfd98bb3.png)


### DnCNN을 사용하여 노이즈 제거
```c
net = denoisingNetwork('dncnn');     % 사전 훈련된 dncnn 네트워크 로드
denoisedR = denoiseImage(noisyR,net);   % 각 채널 노이즈 제거
denoisedG = denoiseImage(noisyG,net);
denoisedB = denoiseImage(noisyB,net);
```

사전 훈련된 네트워크 [DnCNN]

![2021-08-29 (7)](https://user-images.githubusercontent.com/86040099/131248670-59cd781f-4d38-4dab-92f4-d3c66683227f.png)

노이즈 제거된 이미지 [denoisedR,denoisedG,denoisedB]

![2021-08-29 (8)](https://user-images.githubusercontent.com/86040099/131248792-571d04e7-7fa9-4a60-8495-974428f4b670.png)


### 노이즈 제거된 이미지 재결합
```c
denoisedRGB = cat(3,denoisedR,denoisedG,denoisedB);     % 이미지 재결합
imshow(denoisedRGB)
title('Denoised Image')
```

제거된 이미지 [denoisedRGB]

![2021-08-29 (9)](https://user-images.githubusercontent.com/86040099/131248823-9f56affc-a869-4426-adf2-8cd705f07387.png)


### 노이즈 유무 이미지에 대한 피크 신호 대 노이즈 비율(PSNR)을 계산
```c
noisyPSNR = psnr(noisyRGB,pristineRGB);
% pristineRGB를 기준으로 noisyRGB의 피크 신호 대 잡음비를 계산
fprintf('\n The PSNR value of the noisy image is %0.4f.',noisyPSNR);    % 잡음비를 출력
denoisedPSNR = psnr(denoisedRGB,pristineRGB);
fprintf('\n The PSNR value of the denoised image is %0.4f.',denoisedPSNR);
```

노이즈 비율(PSNR) [noisyPSNR,denoisedPSNR]

![2021-08-29 (10)](https://user-images.githubusercontent.com/86040099/131248866-d1b46b42-8ef4-4e52-af5f-31720bbb01b8.png)


### 노이즈 유무 이미지 구조 유사도(SSIM)를 계산
```c
noisySSIM = ssim(noisyRGB,pristineRGB);
% pristineRGB를 참조하여 noisyRGB의 구조 유사도를 계산
fprintf('\n The SSIM value of the noisy image is %0.4f.',noisySSIM);
denoisedSSIM = ssim(denoisedRGB,pristineRGB);
fprintf('\n The SSIM value of the denoised image is %0.4f.',denoisedSSIM);
```

이미지 구조 유사도(SSIM) [noisySSIM,denoisedSSIM]

SSIM 지수가 1에 가까우면 기준 이미지와 잘 일치하고 영상 화질이 더 높다는 것을 의미합니다

![2021-08-29 (11)](https://user-images.githubusercontent.com/86040099/131248901-bdf068e9-9663-4233-94b3-8430d7cfb62a.png)
