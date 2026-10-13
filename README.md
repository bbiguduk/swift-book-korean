# The Swift Programming Language (한국어)

[![Readme Card](https://github-readme-stats.vercel.app/api/pin/?username=bbiguduk\&repo=Swift\_language\_guide\_kr\&theme=midnight-purple)](https://github.com/anuraghazra/github-readme-stats)

## The Swift Programming Language

[The Swift Programming Language](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/)를 한국어로 번역한 것이며, 오역 및 오타 많을 수 있습니다. 발견 시 연락처에 있는 email로 연락해 주세요!

### PDF 다운로드

[Swift 6.4 한국어 가이드 PDF](pdf/swift-book-korean-6.4.pdf)

언어 가이드, 언어 참조와 Swift 6.4 번역자 보충을 포함한 548쪽 PDF입니다. 클릭 가능한 목차와 책갈피, 코드 예제와 삽화를 포함합니다.
2026-10-14에 [한국어 소스 29dcb02](https://github.com/bbiguduk/swift-book-korean/tree/29dcb0261518adb556137ce5b2ef9a127e9a37a6)를 기준으로 생성했습니다.

### 업데이트

* Swift 6.4, 2026-10-13
* Swift 6.3, 2026-04-24
* Swift 6.2, 2025-09-19
* Swift 6, 2024-09-19
* Swift 5.10, 2024-03-08
* Swift 5.9.2, 2023-12-03
* Swift 5.9, 2023-06-19
* Swift 5.8, 2023-04-04
* Swift 5.7, 2022-09-13
* Swift 5.6, 2022-03-17
* Swift 5.5, 2021-08-14
* Swift 5.4, 2021-02-20
* Swift 5.3, 2020-10-23

### 번역 기준과 문서 확인

현재 번역 기준은 [swiftlang/swift-book의 1c059843 커밋](https://github.com/swiftlang/swift-book/tree/1c0598430507e6c2b9a0f068b6dd7c618392fea6)이며, Swift 6.4 책의 내용을 반영합니다.
영문 원본에 없는 최신 언어 설명과 기술적 정정은 [Swift 6.4 번역자 보충](TSPLK.docc/Supplements/Swift64.md)에 구분하여 정리했습니다.

참조 본문의 문법을 수정한 뒤에는 요약을 다시 생성하고 확인합니다:

```sh
python3 bin/generate-grammar
python3 bin/generate-grammar --check
```

DocC 문서는 다음 명령으로 확인할 수 있습니다:

```sh
xcrun docc convert TSPLK.docc --hosting-base-path swift-book-korean --output-path /tmp/swift-book-korean.doccarchive
```
