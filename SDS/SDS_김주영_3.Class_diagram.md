# 3. Class Diagram

---

## 3.7 타이머 기능 Class diagram

### 3.7.1 Entity Class diagram

**타이머 Entity 다이어그램**

<img width="2000" alt="Timer Entity Diagram" src="../images/Timer_Entity_Diagram.png" />

#### PersonalTimer
**Class Description:** 개인 타이머 엔티티. 사용자의 공부 시간을 실시간으로 측정하고 관리하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| id | 타이머 고유 식별자 | Long | private |
| memberId | 사용자 ID | Long | private |
| roomId | 현재 입장한 방 ID | Long | private |
| timerMode | 타이머 모드 (기본 모드 or 뽀모도로 모드) | TimerMode | private |
| timerStatus | 타이머 상태 (공부 중 or 휴식 중) | TimerStatus | private |
| sessionStartTime | 현재 세션 시작 시간 | LocalDateTime | private |
| totalStudySeconds | 총 누적 공부 시간 (초 단위) | Long | private |
| createdAt | 타이머 생성 시간 (방 입장 시간) | LocalDateTime | private |
| updatedAt | 마지막 업데이트 시간 | LocalDateTime | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| toggleStatus | 수동 토글: 공부 ↔ 휴식 전환 (기본 모드에서만 가능) | none | void |
| switchToPomodoroMode | 뽀모도로 모드로 전환 | none | void |
| switchToBasicMode | 기본 모드로 전환 | none | void |
| changePomodoroStatus | 뽀모도로 상태 변경 (공부 ↔ 휴식) | TimerStatus | void |
| accumulateStudyTime | 현재까지의 공부 시간 누적 (private) | none | void |
| endTimer | 타이머 종료 (방 퇴장 시). 마지막 세션 시간 누적 | none | void |
| getCurrentSessionSeconds | 현재 세션의 경과 시간을 초 단위로 반환 | none | long |

---

#### PomodoroSetting
**Class Description:** 뽀모도로 설정 엔티티. 사용자별 공부/휴식 시간 설정을 저장하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| id | 설정 고유 식별자 | Long | private |
| memberId | 사용자 ID (1:1 관계, unique) | Long | private |
| studyMinutes | 공부 시간 (분 단위) | Integer | private |
| restMinutes | 휴식 시간 (분 단위) | Integer | private |
| createdAt | 설정 생성 시간 | LocalDateTime | private |
| updatedAt | 설정 업데이트 시간 | LocalDateTime | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| updateSetting | 뽀모도로 설정 업데이트 | Integer, Integer | void |
| validateMinutes | 시간 유효성 검증 (1~120분 범위). private 메소드 | Integer, Integer | void |
| getStudySeconds | 공부 시간을 초 단위로 반환 | none | long |
| getRestSeconds | 휴식 시간을 초 단위로 반환 | none | long |

---

#### StudyHistory
**Class Description:** 공부 기록 엔티티. 날짜별 누적 공부 시간을 저장하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| id | 기록 고유 식별자 | Long | private |
| memberId | 사용자 ID | Long | private |
| studyDate | 공부한 날짜 | LocalDate | private |
| totalStudySeconds | 해당 날짜의 총 공부 시간 (초 단위) | Long | private |
| createdAt | 기록 생성 시간 | LocalDateTime | private |
| updatedAt | 기록 업데이트 시간 | LocalDateTime | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| addStudyTime | 공부 시간 추가. 0 이상의 값만 허용 | Long | void |
| getFormattedTime | 시간을 "HH:MM:SS" 형식의 문자열로 반환 | none | String |

---

#### TimerMode
**Enum Description:** 타이머 모드 열거형. 기본 모드와 뽀모도로 모드를 구분하는 Enum

**Values:**

| Name | Description |
|------|-------------|
| BASIC | 기본 모드 (수동 토글) |
| POMODORO | 뽀모도로 모드 (자동 전환) |

---

#### TimerStatus
**Enum Description:** 타이머 상태 열거형. 공부 중과 휴식 중 상태를 구분하는 Enum

**Values:**

| Name | Description |
|------|-------------|
| STUDYING | 공부 중 (시간 누적 ON) |
| RESTING | 휴식 중 (시간 누적 OFF) |

---

### 3.7.2 DTO Class diagram

**타이머 DTO 다이어그램**

<img width="2000" alt="Timer DTO Diagram" src="../images/Timer_DTO_Diagram.png" />

#### PomodoroSettingRequest
**Class Description:** 뽀모도로 설정 요청 DTO. 공부/휴식 시간을 설정할 때 사용하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| studyMinutes | 공부 시간 (분 단위) | Integer | private |
| restMinutes | 휴식 시간 (분 단위) | Integer | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| PomodoroSettingRequest | 모든 필드를 초기화하는 생성자 | Integer, Integer | none |
| getStudyMinutes | 공부 시간을 조회하는 Getter | none | Integer |
| getRestMinutes | 휴식 시간을 조회하는 Getter | none | Integer |

---

#### TimerStatusResponse
**Class Description:** 타이머 상태 응답 DTO. 현재 타이머의 전체 정보를 반환하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| timerId | 타이머 ID | Long | private |
| memberId | 사용자 ID | Long | private |
| roomId | 방 ID | Long | private |
| timerMode | 타이머 모드 | TimerMode | private |
| timerStatus | 타이머 상태 | TimerStatus | private |
| currentSessionSeconds | 현재 세션 경과 시간 (초) | Long | private |
| totalStudySeconds | 총 누적 공부 시간 (초) | Long | private |
| totalStudyTime | 총 누적 공부 시간 (HH:MM:SS 형식) | String | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| TimerStatusResponse | PersonalTimer 엔티티로부터 DTO 생성 | PersonalTimer | none |
| formatSeconds | 초를 HH:MM:SS 형식으로 변환. private static 메소드 | Long | String |

---

#### PomodoroSettingResponse
**Class Description:** 뽀모도로 설정 응답 DTO. 저장된 뽀모도로 설정 정보를 반환하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| id | 설정 ID | Long | private |
| memberId | 사용자 ID | Long | private |
| studyMinutes | 공부 시간 (분) | Integer | private |
| restMinutes | 휴식 시간 (분) | Integer | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| PomodoroSettingResponse | PomodoroSetting 엔티티로부터 DTO 생성 | PomodoroSetting | none |

---

#### StudyTimeResponse
**Class Description:** 누적 공부 시간 응답 DTO. 총 누적 시간과 오늘 공부 시간을 반환하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| totalStudySeconds | 총 누적 공부 시간 (초) | Long | private |
| totalStudyTime | 총 누적 공부 시간 (HH:MM:SS 형식) | String | private |
| todayStudySeconds | 오늘 공부 시간 (초) | Long | private |
| todayStudyTime | 오늘 공부 시간 (HH:MM:SS 형식) | String | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| of | 초 단위 시간으로부터 DTO 생성. static factory 메소드 | Long, Long | StudyTimeResponse |
| formatSeconds | 초를 HH:MM:SS 형식으로 변환. private static 메소드 | Long | String |

---

### 3.7.3 Services Class diagram

**타이머 Service 다이어그램**

<img width="2000" alt="Timer Service Diagram" src="../images/Timer_Service_Diagram.png" />

#### PersonalTimerService
**Class Description:** 개인 타이머 비즈니스 로직을 처리하는 Service Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| personalTimerRepository | 개인 타이머 repository | PersonalTimerRepository | private |
| pomodoroSettingRepository | 뽀모도로 설정 repository | PomodoroSettingRepository | private |
| studyHistoryRepository | 공부 기록 repository | StudyHistoryRepository | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| startTimer | 타이머 시작 (방 입장 시). 모든 참여자는 공부 상태로 시작 | Long, Long, boolean | TimerStatusResponse |
| endTimer | 타이머 종료 (방 퇴장 시). 누적 시간을 StudyHistory에 저장하고 타이머 삭제 | Long | void |
| toggleTimer | 수동 토글 (공부 ↔ 휴식). 기본 모드에서만 작동 | Long | TimerStatusResponse |
| startPomodoroMode | 뽀모도로 모드 시작. 뽀모도로 설정이 필요함 | Long | TimerStatusResponse |
| stopPomodoroMode | 뽀모도로 모드 종료. 기본 모드로 전환 | Long | TimerStatusResponse |
| changePomodoroStatus | 뽀모도로 상태 자동 전환 (공부 ↔ 휴식) | Long, TimerStatus | TimerStatusResponse |
| getTimerStatus | 현재 타이머 상태 조회 | Long | TimerStatusResponse |
| getStudyTime | 누적 시간 조회 (총 누적 시간 + 오늘 공부 시간) | Long | StudyTimeResponse |
| saveToStudyHistory | StudyHistory에 공부 시간 저장. private 메소드 | Long, Long | void |

---

#### PomodoroSettingService
**Class Description:** 뽀모도로 설정 비즈니스 로직을 처리하는 Service Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| pomodoroSettingRepository | 뽀모도로 설정 repository | PomodoroSettingRepository | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| saveSetting | 뽀모도로 설정 저장/업데이트. 첫 설정 시 생성, 이미 있으면 업데이트 | Long, PomodoroSettingRequest | PomodoroSettingResponse |
| getSetting | 뽀모도로 설정 조회. 설정이 없으면 null 반환 | Long | PomodoroSettingResponse |
| hasSettings | 뽀모도로 설정 존재 여부 확인 | Long | boolean |

---

### 3.7.4 Controllers Class diagram

**타이머 Controller 다이어그램**

<img width="2000" alt="Timer Controller Diagram" src="../images/Timer_Controller_Diagram.png" />

#### PersonalTimerController
**Class Description:** 개인 타이머 API 엔드포인트를 제공하는 Controller Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| personalTimerService | 개인 타이머 서비스 | PersonalTimerService | private |
| pomodoroSettingService | 뽀모도로 설정 서비스 | PomodoroSettingService | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| startTimer | 타이머 시작 (방 입장 시). POST /api/timer/start | CustomUser, Long, boolean | ResponseEntity\<TimerStatusResponse\> |
| endTimer | 타이머 종료 (방 퇴장 시). POST /api/timer/end | CustomUser | ResponseEntity\<Void\> |
| toggleTimer | 수동 토글 (공부 ↔ 휴식). POST /api/timer/toggle | CustomUser | ResponseEntity\<TimerStatusResponse\> |
| startPomodoroMode | 뽀모도로 모드 시작. POST /api/timer/pomodoro/start | CustomUser | ResponseEntity\<TimerStatusResponse\> |
| stopPomodoroMode | 뽀모도로 모드 종료. POST /api/timer/pomodoro/stop | CustomUser | ResponseEntity\<TimerStatusResponse\> |
| changePomodoroStatus | 뽀모도로 상태 전환. POST /api/timer/pomodoro/change-status | CustomUser, TimerStatus | ResponseEntity\<TimerStatusResponse\> |
| getTimerStatus | 현재 타이머 상태 조회. GET /api/timer/status | CustomUser | ResponseEntity\<TimerStatusResponse\> |
| getStudyTime | 누적 공부 시간 조회. GET /api/timer/study-time | CustomUser | ResponseEntity\<StudyTimeResponse\> |
| savePomodoroSetting | 뽀모도로 설정 저장. POST /api/timer/pomodoro/settings | CustomUser, PomodoroSettingRequest | ResponseEntity\<PomodoroSettingResponse\> |
| getPomodoroSetting | 뽀모도로 설정 조회. GET /api/timer/pomodoro/settings | CustomUser | ResponseEntity\<PomodoroSettingResponse\> |

---

### 3.7.5 Repository Class diagram

**타이머 Repository 다이어그램**

<img width="2000" alt="Timer Repository Diagram" src="../images/Timer_Repository_Diagram.png" />

#### PersonalTimerRepository
**Class Description:** PersonalTimer 엔티티용 repository, 사용자/방 기준 조회 기능을 제공하는 Class

**Attributes:** None

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| findByMemberId | 사용자의 활성 타이머 조회 | Long | Optional\<PersonalTimer\> |
| existsByMemberId | 사용자의 활성 타이머 존재 여부 확인 | Long | boolean |
| findByRoomId | 특정 방의 모든 타이머 조회 | Long | List\<PersonalTimer\> |
| deleteByMemberId | 사용자의 활성 타이머 삭제 | Long | void |

---

#### PomodoroSettingRepository
**Class Description:** PomodoroSetting 엔티티용 repository, 사용자 기준 조회 기능을 제공하는 Class

**Attributes:** None

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| findByMemberId | 사용자의 뽀모도로 설정 조회 | Long | Optional\<PomodoroSetting\> |
| existsByMemberId | 사용자의 뽀모도로 설정 존재 여부 확인 | Long | boolean |

---

#### StudyHistoryRepository
**Class Description:** StudyHistory 엔티티용 repository, 날짜별 조회 및 누적 시간 계산 기능을 제공하는 Class

**Attributes:** None

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| findByMemberIdAndStudyDate | 특정 날짜의 공부 기록 조회 | Long, LocalDate | Optional\<StudyHistory\> |
| findByMemberIdOrderByStudyDateDesc | 사용자의 모든 공부 기록 조회 (최신순) | Long | List\<StudyHistory\> |
| findByMemberIdAndStudyDateBetween | 사용자의 특정 기간 공부 기록 조회 | Long, LocalDate, LocalDate | List\<StudyHistory\> |
| getTotalStudySecondsByMemberId | 사용자의 총 누적 공부 시간 계산 (초). @Query 사용 | Long | Long |
| getTotalStudySecondsByMemberIdAndDateBetween | 특정 기간 누적 공부 시간 계산 (초). @Query 사용 | Long, LocalDate, LocalDate | Long |

---

## 3.8 채팅 기능 Class diagram

### 3.8.1 Entity Class diagram

**채팅 Entity 다이어그램**

<img width="2000" alt="Chat Entity Diagram" src="../images/Chat_Entity_Diagram.png" />

#### ChatMessage
**Class Description:** 채팅 메시지 엔티티. 스터디방의 채팅 메시지를 저장하고 관리하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| id | 메시지 고유 식별자 | Long | private |
| roomType | 방 타입 (OPEN, GROUP) | ChatRoomType | private |
| roomId | 방 번호 | Long | private |
| sender | 보낸 사람 | String | private |
| message | 메시지 내용 | String | private |
| type | 메시지 타입 | MessageType | private |
| sentAt | 메시지 전송 시간 | LocalDateTime | private |
| refId | 답변 원본 질문 ID | Long | private |
| isSolved | 해결 여부 | Boolean | private |
| isSelected | 채택 여부 | Boolean | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| markAsSolved | 질문을 해결 상태로 변경 | none | void |
| markAsSelected | 답변을 채택 상태로 변경 | none | void |

---

#### ChatRoomType
**Enum Description:** 채팅방 타입 열거형. 오픈스터디와 그룹스터디를 구분하는 Enum

**Values:**

| Name | Description |
|------|-------------|
| OPEN | 오픈스터디 |
| GROUP | 그룹스터디 |

---

#### MessageType
**Enum Description:** 메시지 타입 열거형. 채팅 메시지의 종류를 구분하는 Enum

**Values:**

| Name | Description |
|------|-------------|
| ENTER | 입장 메시지 |
| TALK | 일반 대화 |
| LEAVE | 퇴장 메시지 |
| IMAGE | 이미지 메시지 |
| QUESTION | 질문 메시지 |
| ANSWER | 답변 메시지 |
| SOLVE | 해결 메시지 |

---

### 3.8.2 DTO Class diagram

**채팅 DTO 다이어그램**

<img width="2000" alt="Chat DTO Diagram" src="../images/Chat_DTO_Diagram.png" />

#### ChatMessageRequest
**Class Description:** 채팅 메시지 요청 DTO. 클라이언트가 메시지를 전송할 때 사용하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| type | 메시지 타입 | MessageType | private |
| roomType | 방 타입 | ChatRoomType | private |
| roomId | 방 번호 | Long | private |
| message | 메시지 내용 | String | private |
| refId | 답변일 경우 질문 ID (옵션) | Long | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| getType | 메시지 타입을 조회하는 Getter | none | MessageType |
| getRoomType | 방 타입을 조회하는 Getter | none | ChatRoomType |
| getRoomId | 방 번호를 조회하는 Getter | none | Long |
| getMessage | 메시지 내용을 조회하는 Getter | none | String |
| getRefId | 참조 질문 ID를 조회하는 Getter | none | Long |
| setType | 메시지 타입을 설정하는 Setter | MessageType | void |
| setRoomType | 방 타입을 설정하는 Setter | ChatRoomType | void |
| setRoomId | 방 번호를 설정하는 Setter | Long | void |
| setMessage | 메시지 내용을 설정하는 Setter | String | void |
| setRefId | 참조 질문 ID를 설정하는 Setter | Long | void |

---

#### ChatMessageResponse
**Class Description:** 채팅 메시지 응답 DTO. 서버가 클라이언트에게 메시지 정보를 반환하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| messageId | 메시지 ID | Long | private |
| type | 메시지 타입 | MessageType | private |
| roomType | 방 타입 | ChatRoomType | private |
| roomId | 방 번호 | Long | private |
| sender | 서버가 인증정보로 채워 넣은 진짜 보낸 사람 | String | private |
| message | 메시지 내용 | String | private |
| sentAt | 전송 시간 | LocalDateTime | private |
| refId | 참조 질문 ID | Long | private |
| isSolved | 해결 여부 | Boolean | private |
| isSelected | 채택 여부 | Boolean | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| from | ChatMessage 엔티티로부터 DTO 생성. static factory 메소드 | ChatMessage | ChatMessageResponse |

---

### 3.8.3 Services Class diagram

**채팅 Service 다이어그램**

<img width="2000" alt="Chat Service Diagram" src="../images/Chat_Service_Diagram.png" />

#### ChatService
**Class Description:** 채팅 비즈니스 로직을 처리하는 Service Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| chatRepository | 채팅 메시지 repository | ChatRepository | private |
| memberRepository | 회원 repository | MemberRepository | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| saveMessage | 메시지 저장. 입장/질문/답변 타입별 검증 및 처리 | ChatMessageRequest, String | ChatMessageResponse |
| solveQuestion | 질문 해결 완료 처리. 선택적으로 답변 채택 및 채택수 증가 | Long, Long, String | ChatMessage |
| deleteMessage | 메시지 삭제. 작성자만 삭제 가능 | Long, String | void |
| getChatHistory | 채팅 내역 조회. 페이징 처리하여 최신순으로 반환 | Long, ChatRoomType, int, int | List\<ChatMessage\> |

---

### 3.8.4 Controllers Class diagram

**채팅 Controller 다이어그램**

<img width="2000" alt="Chat Controller Diagram" src="../images/Chat_Controller_Diagram.png" />

#### ChatController
**Class Description:** 채팅 API 및 WebSocket 엔드포인트를 제공하는 Controller Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| messagingTemplate | WebSocket 메시지 전송 템플릿 | SimpMessageSendingOperations | private |
| chatService | 채팅 서비스 | ChatService | private |
| s3Service | S3 파일 업로드 서비스 | S3Service | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| message | 메시지 전송 처리. WebSocket /pub/chat/message | ChatMessageRequest, Principal | void |
| solveQuestion | 질문 해결 및 답변 채택. PATCH /api/chat/message/{messageId}/solve | Long, Long, CustomUser | ResponseEntity\<String\> |
| deleteMessage | 채팅 메시지 삭제. DELETE /api/chat/message/{messageId} | Long, CustomUser | ResponseEntity\<String\> |
| getChatHistory | 채팅 내역 조회. GET /api/chat/room/{roomId} | Long, ChatRoomType, int, int | ResponseEntity\<List\<ChatMessage\>\> |
| uploadChatImage | 채팅 이미지 업로드. POST /api/chat/image | MultipartFile | ResponseEntity\<String\> |

---

### 3.8.5 Repository Class diagram

**채팅 Repository 다이어그램**

<img width="2000" alt="Chat Repository Diagram" src="../images/Chat_Repository_Diagram.png" />

#### ChatRepository
**Class Description:** ChatMessage 엔티티용 repository, 방별 메시지 조회 기능을 제공하는 Class

**Attributes:** None

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| findByRoomIdAndRoomTypeOrderBySentAtDesc | 특정 방의 채팅 내역을 최신순으로 조회. 페이징 지원 | Long, ChatRoomType, Pageable | Slice\<ChatMessage\> |

---

## 3.9 스터디 세션 기능 Class diagram

### 3.9.1 Entity Class diagram

**스터디 세션 Entity 다이어그램**

<img width="2000" alt="Session Entity Diagram" src="../images/Session_Entity_Diagram.png" />

#### StudySession
**Class Description:** 공부 세션 엔티티. 회원이 스터디방에서 공부한 시간을 기록하고 관리하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| id | 세션 고유 식별자 | Long | private |
| member | 공부하는 회원 | Member | private |
| studyType | 스터디 타입 ("OPEN_STUDY" 또는 "GROUP_STUDY") | String | private |
| roomId | 스터디방 ID (오픈스터디 또는 그룹스터디의 방 ID) | Long | private |
| startTime | 세션 시작 시간 | LocalDateTime | private |
| endTime | 세션 종료 시간 (null이면 아직 진행 중) | LocalDateTime | private |
| mode | 현재 모드 ("STUDY" 또는 "REST") | String | private |
| lastModeChangeTime | 마지막 모드 변경 시간 | LocalDateTime | private |
| studyMinutes | 실제 공부한 시간 (분 단위) | Integer | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| addStudyMinutes | 공부 시간 추가 (분 단위) | int | void |
| endSession | 세션 종료 | none | void |
| isActive | 세션이 진행 중인지 확인 | none | boolean |

---

### 3.9.2 DTO Class diagram

**스터디 세션 DTO 다이어그램**

<img width="2000" alt="Session DTO Diagram" src="../images/Session_DTO_Diagram.png" />

#### SessionStartRequestDto
**Class Description:** 세션 시작 요청 DTO. 공부 세션 시작 시 사용하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| studyType | 스터디 타입 ("OPEN_STUDY" 또는 "GROUP_STUDY") | String | private |
| roomId | 스터디방 ID | Long | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| studyType | 스터디 타입을 조회하는 accessor | none | String |
| roomId | 방 ID를 조회하는 accessor | none | Long |

---

#### SessionResponseDto
**Class Description:** 세션 응답 DTO. 공부 세션 정보를 반환하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| sessionId | 세션 ID | Long | private |
| memberId | 회원 ID | Long | private |
| studyType | 스터디 타입 | String | private |
| roomId | 방 ID | Long | private |
| mode | 현재 모드 | String | private |
| studyMinutes | 공부 시간 (분) | Integer | private |
| startTime | 시작 시간 | LocalDateTime | private |
| endTime | 종료 시간 | LocalDateTime | private |
| isActive | 활성 상태 여부 | Boolean | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| from | StudySession 엔티티로부터 DTO 생성. static factory 메소드 | StudySession | SessionResponseDto |

---

#### SessionEndResultDto
**Class Description:** 세션 종료 결과 DTO. 세션 종료 시 레벨업 결과를 포함하여 반환하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| sessionId | 세션 ID | Long | private |
| studyMinutes | 총 공부 시간 (분) | Integer | private |
| leveledUp | 레벨업 여부 | Boolean | private |
| newLevel | 새 레벨 (레벨업 안했으면 null) | Integer | private |

**Operations:** None (record class)

---

#### LevelInfoDto
**Class Description:** 레벨 정보 DTO. 회원의 레벨 관련 상세 정보를 반환하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| memberId | 회원 ID | Long | private |
| username | 회원 이름 | String | private |
| currentLevel | 현재 레벨 | Integer | private |
| totalExp | 총 누적 경험치 (분) | Integer | private |
| currentLevelExp | 현재 레벨에서 획득한 경험치 (분) | Integer | private |
| requiredExpForNextLevel | 다음 레벨까지 필요한 총 경험치 (분) | Integer | private |
| remainingExp | 다음 레벨까지 남은 경험치 (분) | Integer | private |
| progress | 현재 레벨 진행률 (0~100%) | Double | private |

**Operations:** None (record class)

---

### 3.9.3 Services Class diagram

**스터디 세션 Service 다이어그램**

<img width="2000" alt="Session Service Diagram" src="../images/Session_Service_Diagram.png" />

#### StudySessionService
**Class Description:** 공부 세션 관리 서비스. Timer와 연동하여 실제 공부 시간을 측정하고 레벨업 처리하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| studySessionRepository | 스터디 세션 repository | StudySessionRepository | private |
| memberRepository | 회원 repository | MemberRepository | private |
| levelUpService | 레벨업 서비스 | LevelUpService | private |
| personalTimerRepository | 개인 타이머 repository | PersonalTimerRepository | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| startStudySession | 공부 세션 시작 (공부 모드로 시작) | Long, String, Long | StudySession |
| endStudySession | 공부 세션 종료 및 레벨업 처리. Timer에서 실제 공부 시간 가져와서 처리 | Long | SessionEndResultDto |
| getActiveSession | 회원의 활성 세션 조회 | Long | StudySession |

---

#### LevelUpService
**Class Description:** 레벨업 서비스. 공부 시간을 경험치로 변환하고 레벨업을 처리하는 Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| memberRepository | 회원 repository | MemberRepository | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| getRequiredMinutesForLevel | 특정 레벨에서 다음 레벨로 올라가기 위해 필요한 시간 계산 (분 단위). 레벨의 10의 자리수 + 1 × 10시간 | int | int |
| calculateTotalExpForLevel | 특정 레벨에 도달하기 위해 필요한 총 누적 경험치 계산. private 메소드 | int | int |
| addStudyTimeAndCheckLevelUp | 공부 시간 추가 및 레벨업 처리. 여러 레벨 한번에 올라갈 수 있음 | Long, int | boolean |
| getLevelInfo | 회원의 레벨 정보 상세 조회 | Long | LevelInfoDto |

---

### 3.9.4 Controllers Class diagram

**스터디 세션 Controller 다이어그램**

<img width="2000" alt="Session Controller Diagram" src="../images/Session_Controller_Diagram.png" />

#### StudySessionController
**Class Description:** 공부 세션 및 레벨업 API 엔드포인트를 제공하는 Controller Class

**Attributes:**

| Name | Description | Type | Visibility |
|------|-------------|------|------------|
| studySessionService | 스터디 세션 서비스 | StudySessionService | private |
| levelUpService | 레벨업 서비스 | LevelUpService | private |

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| startSession | 공부 세션 시작. POST /api/study-sessions/start | CustomUser, SessionStartRequestDto | ResponseEntity\<SessionResponseDto\> |
| endSession | 공부 세션 종료 및 레벨업 처리. POST /api/study-sessions/{sessionId}/end | Long | ResponseEntity\<SessionEndResultDto\> |
| getActiveSession | 내 활성 세션 조회. GET /api/study-sessions/active | CustomUser | ResponseEntity\<SessionResponseDto\> |
| getLevelInfo | 내 레벨 정보 조회. GET /api/study-sessions/level | CustomUser | ResponseEntity\<LevelInfoDto\> |

---

### 3.9.5 Repository Class diagram

**스터디 세션 Repository 다이어그램**

<img width="2000" alt="Session Repository Diagram" src="../images/Session_Repository_Diagram.png" />

#### StudySessionRepository
**Class Description:** StudySession 엔티티용 repository, 회원별 활성 세션 조회 기능을 제공하는 Class

**Attributes:** None

**Operations:**

| Name | Description | Argument | Returns |
|------|-------------|----------|---------|
| findByMemberIdAndEndTimeIsNull | 회원의 활성 세션 조회 (종료되지 않은 세션) | Long | Optional\<StudySession\> |

---
