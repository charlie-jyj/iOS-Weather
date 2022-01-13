## Weather Cast
> 날씨 앱 만들기

### 1) 기능
- 도시 이름 입력 시 현재 날씨 정보를 가져와 화면에 표시
- 도시 이름 잘못 입력 시 서버로 부터 응답받은 에러 메시지가 alert

### 2) 기본 개념
#### (1) Current Weather API

#### (2) URLSession
> HTTP 통신 : Hyper Text Transfer Protocol
- stateless
- connectionless
- HTTP Packet = header + body
- HTTP method
- HTTP Status

<img src="https://letsean.dev/img/http/1.png">
<img src="https://miro.medium.com/max/1400/1*w_iicbG7L3xEQTArjHUS6g.jpeg">

*URLSession=> 서버와 통신하기 위해 제공하는 API
특정한 url을 이용하여 데이터를 다운로드하고 업로드하기 위해
App 은 하나 이상의 url session instance 를 생성
각 인스턴스는 관련 데이터 전송자 그룹을 조종
하나의 tab/window : 하나의 session
- request: 메서드 결정, 정책 지정
- response

##### URLSessionConfiguration
객체 생성
Session property 설정
Configuration property 에 따라서 session 의 종류가 결정된다

- 공유 세션 (Shared Session)
    - URLSession.shared()
    - singleton, 기본 요청
    - 맞춤 설정은 어렵지만 쉬운 사용
- 기본 세션 (Default Session)
    - 공유 세션과 유사하나 맞춤 사용 가능
    - URLSession(configuration: .default)
    - 캐시, 쿠키 인증을 디스크에 저장
    - 순차적인 데이터 처리 위한 delegate 지정 가능
- 임시 세션 (Ephemeral Session)
    - 캐시, 쿠키, 사용자 정보 저장하지 않는다
    - URLSession(configuration: .ephemeral)
    - 메모리에서 세션 생성, 세션 삭제 시 데이터 사라짐
- 백그라운드 세션 (Background Session)
    - URLSession(configuration: .background)
    - 앱이 실행되지 않는 동안 백그라운드에서 컨텐츠 다운로드/ 업로드 수행

##### URLSessionTask
Url session 객체는 하나 이상의 task 객체를 생성하고 이를 통해 서버와 통신

- URLSessionDataTask
    - 데이터 객체, 데이터 요청 응답, 짧고 빈번한 요청
- URLSessionUploadTask
    - 데이터 객체, 파일 업로드, 앱 실행되지 않을 때 백그라운드 업로드 지원
- URLSessionDownloadTask
    - 다운로드 받아 파일로 저장, 앱 실행되지 않을 때 백그라운드 다운로드 지원
- URLSessionStreamTask
    - TCP/IP 연결
- URLSessionWebSocketTask

##### URLSession Life Cycle
1. Session configuration 결정 => session 생성
2. 통신할 url, request 객체 설정
3. 사용할 task 결정 그에 맞는 completion handler, delegate method 작성
4. 해당 task 실행
5. Task 완료 후 Completion handler 클로저 호출

https://developer.apple.com/documentation/foundation/urlsession


### 3) 새롭게 배운 것

- Codable
A type that can covert itself into and out of an external representation
Is a type alias for the Encodable and Decodable protocols 
It matches any type that conforms to both protocols

Make your data types encodable and decodable for compatibility with external representations
Such as JSON
    - json 데이터를 decode 하기 위해서는 key 의 이름과 value type 이 일치하게 struct를 define 
    - 다르게 사용하고 싶으면 coding key protocol을 adopt
    - coding keys enum (string type)

https://developer.apple.com/documentation/swift/codable
https://developer.apple.com/documentation/foundation/archives_and_serialization/encoding_and_decoding_custom_types

Codable types can declare a special nested enumeration named “CodingKeys” that conforms to the CodingKeyProtocol. When this enumeration is present, tis cases serve at the authoritative list of properties that muse be included when instances of a coddle type are encoded or decoded. 

The names of the enumeration cases should match the names you’ve given to the corresponding properties in your type

```swift
struct Landmark: Codable {
    var name: String
    var foundingYear: Int
    var location: Coordinate
    var vantagePoints: [Coordinate]
    
    enum CodingKeys: String, CodingKey {
        case name = "title"
        case foundingYear = "founding_date"
        
        case location
        case vantagePoints
    }
}

```

- trailing closure 
후행 클로저
If you need to pass a closure expression to a function as the functions final argument and the closure expressions is long, it can be useful to write it as a trailing closure instead.
You write a trailing closure after the function call’s parenthesis

- JSONDecoder()

- try catch
https://codewithchris.com/swift-try-catch/

- session.dataTask()
Creates a task that retrieves the contents of a URL based on the specified URL request object, 
And calls a handler upon completion 

    - return => the new session data task: URLSessionDataTask
    - https://developer.apple.com/documentation/foundation/urlsessiontask
    - after you create a task, you start It by calling its resume() method. 
    - the session then maintains a strong reference to the task until the request finishes or fails

- editing 중 일 때는 키보드가 나타나는데 
키보드 없애기 위해서는 

```swift
self.view.endEditing(true)
```

- self.view vs self.content view
    - content view : the content view of the cell object
    - view: the view that the controller manages

- Array().first(where: {}) -> Element?
Returns the first element of the sequence that satisfies the given predicate
- Array().last

- network 작업은 별도의 thread 에서 이루어지고 응답이 온다고 해도 main thread 와 관련이 없기 때문에
(URLSessionAPI is highly asynchronous)
dataTask 의 completion closure 에서 UI 작업을 해야 한다면,
Main thread 에서 작업을 하도록 만들어 주어야 한다.

```swift
DispatchQueue.main.async
```
- DispatchQueue
> an object that manages the execution of tasks serially or concurrently on your app’s main thread or on a backgroud thread
    - FIFO queues to which your app can submit tasks in the form of block objects
    - execute tasks either serially or concurrently
     - func aysnc : schedules a work item for immediate execution and returns immediately 

https://developer.apple.com/documentation/dispatch/dispatchqueue

- 크롬 개발자 도구 켜기 : option + command + i

- swift range
A half-open interval from a lower bound up to, but not including, an upper bound

```swift
Let underFive = 0.0..<5.0
underFive.contains(3.0) // true
For n in 3..<5 {
print(n)
}
```
