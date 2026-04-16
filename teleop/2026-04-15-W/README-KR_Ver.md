## 연구 노트 ##
- Info에 권한 문구 추가: Privacy - Hands Tracking Usage description를 타깃 설정에서 Info 탭으로 가서 새 키 추가 함 > 근데 원래 이게 여기서(Xcode UI) 추가하면 키 형태가 올바르게 바뀌면서 추가 되어야하는데 Info 파일에서 소스 코드 확인하니 키 형태가 잘못된 걸을 확인함 소스 코드 수정 후 실행하면 권한 팝업 잘 뜸 

- 그 후 Immersive Space를 열어야 손 트래킹이 작동하기 때문에 Content View에서 Immersive Space를 열도록 수정함
로컬 좌표를 월드 좌표로 변환
Immersive Space가 열리긴 하는데 좌표가 0으로 찍힘 (핸드트랙킹매니저 파일 수정)
—> anchorFromJointTransform은 앵커 기준 로컬 좌표라 0에 가까울 수 있다는 것을 확인 —> 월드 좌표로 바꾸기
anchorFromJointTransform를 originFromAnchorTransform으로 바꿈
즉, 행렬 곱셈으로 좌표계를 로컬 > 월드로 변환함

- ImmersiveView에서 overlay로 텍스트를 표시하려 했는데 Immersive Space는 전체가 3D 공간이라 2D overlay가 제대로 렌더링 되지 않은듯 했음 —> Immersive View는 Immersive Space를 여는 역할만 하도록 하고 UI 표시는 Content View의 윈도우에서 담당하도록 분리
Immersive Space만 열고 manager.start()는 Immersive View의 .task에서 호출함 —> Immersive View의 manager와 Content View의 manager가 별개 인스턴스라서, Immersive View에서 받은 데이터가 Content view 화면에 반영되지 않았음
—>
ContentView가 직접 소유한 manager에서 start()를 호출하므로, 데이터가 들어오면 같은 인스턴스의 leftWristPosition/rightWristPosition이 업데이트되고, SwiftUI가 화면을 갱신하게 함. openImmersiveSpace를 먼저 호출하는 이유는 Immersive Space가 열려야 ARKit 손 트래킹이 실제로 동작하기 때문
> 이제 Xcode 로그에도 잘 뜨고 AVP 화면 상에도 잘 뜸
