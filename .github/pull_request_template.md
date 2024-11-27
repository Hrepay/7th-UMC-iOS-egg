## PR 유형
- [ ] 기능 추가
- [ ] 버그 수정
- [ ] 코드 개선
- [ ] 의존성 업데이트
- [ ] 문서 수정
- [x] UI/UX 개선

## 변경 사항
### 결제 화면 UI 개선 및 성능 최적화

#### UI/UX 개선
- 결제 수단 선택 UI를 카드 형태로 변경
- 결제 버튼 위치 조정 및 강조
- 다크모드 대응 색상 조정
- Dynamic Type 지원 추가

#### 성능 최적화
- 결제 수단 목록 로딩 시 메모리 사용량 20% 감소
- 스크롤 성능 개선을 위한 셀 재사용 메커니즘 수정
- 이미지 캐싱 로직 개선

## 변경된 주요 코드
```swift
final class PaymentMethodCell: UICollectionViewCell {
    private lazy var containerView: UIView = {
        let view = UIView()
        view.layer.cornerRadius = 12
        view.layer.shadowRadius = 4
        view.layer.shadowOpacity = 0.1
        view.layer.shadowOffset = CGSize(width: 0, height: 2)
        return view
    }()
    
    override func prepareForReuse() {
        super.prepareForReuse()
        imageView.cancelDownload()
        imageView.image = nil
    }
}
```

## 테스트 항목
- [x] 다크모드 전환 시 UI 확인
- [x] 다양한 디바이스 해상도 대응 확인
- [x] VoiceOver 작동 확인
- [x] 메모리 누수 확인
- [x] 유닛 테스트 추가
- [x] UI 테스트 추가

## 스크린샷
| 변경 전 | 변경 후 |
|---------|---------|
| <before_screenshot> | <after_screenshot> |

## 영향 범위
- `PaymentViewController`
- `PaymentMethodCell`
- `PaymentManager`
- `ImageCacheManager`

## 리뷰어 체크리스트
- [ ] 네이밍이 명확한가?
- [ ] 책임과 역할이 적절히 분리되었는가?
- [ ] 테스트 커버리지는 충분한가?
- [ ] 에러 처리가 적절한가?
- [ ] UI/UX 가이드라인을 준수하였는가?
- [ ] 메모리 관리가 적절한가?

## 관련 이슈
- Closes #123
- Related to #456

## QA 요청사항
1. 결제 수단 선택 화면에서 스크롤 시 버벅임이 있는지 확인
2. 카드 등록 후 목록 갱신이 즉시 되는지 확인
3. 오프라인 상태에서의 동작 확인

## 배포 시 유의사항
- 새로운 환경변수 `PAYMENT_API_VERSION` 추가 필요
- 결제 모듈 버전 2.0.0으로 업데이트 필요

## 기타 참고사항
- API 문서: [결제 모듈 API v2.0.0](link)
- 디자인 시안: [Figma](link)
