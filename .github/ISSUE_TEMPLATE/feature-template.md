---
name: iOS 버그 리포트
about: iOS 앱의 버그를 보고하기 위한 템플릿입니다
title: '[iOS BUG] 채팅방 메시지 전송 시 앱 크래시 발생'
labels: 'bug, ios, crash'
assignees: 'ios-team-lead'
---

## 버그 설명
채팅방에서 이미지가 포함된 메시지를 전송할 때 간헐적으로 앱이 크래시되는 현상이 발생합니다. 특히 이미지 크기가 5MB 이상일 때 더 자주 발생하는 것으로 보입니다.

## 재현 방법
1. 채팅방 입장 (1:1 채팅방 또는 그룹채팅방 모두 해당)
2. 이미지 첨부 버튼 클릭
3. 사진 라이브러리에서 5MB 이상의 이미지 선택
4. '전송' 버튼 탭
5. 로딩 인디케이터가 표시되다가 앱 크래시 발생

## 크래시 로그
```swift
Fatal Exception: NSInvalidArgumentException
*** -[__NSArrayM insertObject:atIndex:]: object cannot be nil
(
    0   CoreFoundation                      0x00000001834d9e50
    1   libobjc.A.dylib                     0x0000000182f48538
    2   ChatModule                          0x0000000102d4a7f4
    3   ChatModule                          0x0000000102d4a2c8
    4   ChatViewController                  0x0000000102d4a1b0
)
```

## 디바이스 정보
- iOS 버전: 17.1.1
- 디바이스: iPhone 15 Pro
- 앱 버전: 2.5.0 (빌드 번호: 257)
- 발생 빈도: 대용량 이미지 전송 시 약 70% 확률로 발생

## 기대 동작
- 이미지 크기와 관계없이 메시지가 정상적으로 전송되어야 함
- 필요한 경우 이미지 압축 처리가 수행되어야 함
- 전송 실패 시에도 앱 크래시가 아닌 적절한 에러 메시지 표시 필요

## 관련 코드
```swift
func sendImageMessage(_ image: UIImage) {
    guard let imageData = image.jpegData(compressionQuality: 0.8) else {
        // Error handling 누락
        return
    }
    
    chatService.sendMessage(with: imageData) { [weak self] result in
        switch result {
        case .success(let message):
            self?.messageList.append(message) // 크래시 발생 지점
        case .failure(let error):
            // Error handling 미흡
            break
        }
    }
}
```

## 해결 방향 제안
1. 이미지 전송 전 사이즈 체크 및 압축 처리 로직 추가
2. messageList 접근 시 thread-safety 확보
3. 적절한 에러 핸들링 추가

## 체크리스트
- [x] 크래시 로그 첨부
- [x] 재현 방법 상세 기술
- [x] 디바이스 및 OS 정보 제공
- [x] 관련 코드 영역 식별
- [ ] 다른 iOS 버전에서도 테스트 필요
- [ ] 네트워크 상태에 따른 영향 확인 필요

## 참고 사항
- Jira 티켓: CHAT-1234
- 관련 PR: #123, #124
- 슬랙 스레드: https://workspace.slack.com/archives/...
