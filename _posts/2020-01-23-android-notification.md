---
title: "[Android] Android Notification"
date: 2020-01-23
categories: Android, Java
---
# 안드로이드 Notification

앱에서 푸시 알림을 받는 방법은 

1. 앱에서 알림을 받을 환경을 만들어 놓은 뒤
2. FCM 등을 이용한 푸시 서버에서 해당 앱으로 푸시 알림을 보냄. 

# 앱에서 알림 구현

앱에서 알림 채널 및 알림의 노출 형태등을 설정할 수 있고,
앱 내의 이벤트로 해당 알림이 어떻게 동작하는지 푸시 서버 없이 테스트 해볼 수도 있음. (각 기기의 푸시 테스트도 독립적으로 수행 가능)

## 헤즈업 알림

- 5.0 이상부터 사용가능
- 알림이 올 시, 화면상에 잠시 나타났다 사라지는 알림.

## 잠금화면 알림 표시

- 5.0 이상부터 사용가능

## 알림 배지

- 8.0 이상부터 사용가능
- 알림 배지 기능을 지원하는 런처 앱에서 기본적으로 노출하고 있음.
- 설정에서 알림 표시 기능을 사용할 시, 알림 배지가 있는 앱을 길게 터치하면 알림을 모아볼 수 있음.
- 알림 배지 제어

``` java
NotificationChannel channel = new NotificationChannel(CHANNEL_ID, CHANNEL_NAME, NotificationManager.IMPORTANCE_HIGH);
            channel.setShowBadge(true);                         // 아이콘 배지 노출 여부 설정
```

## 알림 작업

- 7.0 이상부터, 텍스트 입력 및 답장 기능 사용 가능.
- 10 이상부터, 알람에 액션 버튼을 생성 가능.

## 확장형 알림

- 알림을 아래로 스와이프 시, 템플릿화 한 내용을 추가로 노출 가능.

### 큰 이미지 확장형 알림

- 알림을 펼쳤을 때, 큰 이미지를 노출
- 필요값 : 일반 제목, 일반 내용 텍스트, 작은 아이콘(큰 이미지를 넣어도 됨), 확장형 제목, 확장형 요약 텍스트, 큰 이미지

```java
Bitmap largeBitmapImage = ((BitmapDrawable)getResources().getDrawable(R.drawable.banner_image)).getBitmap();
                builder.setContentText(PUSH_TEST_CONTENT_3)
                        .setLargeIcon(largeBitmapImage)                                     // 접혀있을 때 큰 아이콘
                        .setStyle(new NotificationCompat.BigPictureStyle()
                                        .bigPicture(largeBitmapImage)                       // 펼쳐졌을 때 보이는 이미지
                                        .bigLargeIcon(null)                                 // 펼쳐졌을 때 큰 아이콘 감추기
                                        .setBigContentTitle(PUSH_TEST_LARGE_TITLE_3)        // 펼쳐졌을 때 큰 텍스트 블록 제목
                                        .setSummaryText(PUSH_TEST_LARGE_SUMMARY_3)          // 펼쳐졌을 때 큰 텍스트 블록 요약
                        );
```

### 큰 텍스트 블록 확장형 알림

- 알림을 펼쳤을 때, 큰 텍스트 블록을 노출
- 필요값 : 일반 제목, 일반 내용 텍스트, 확장형 제목, 확장형 요약 텍스트, 확장형 내용 텍스트

``` java
builder.setContentText(PUSH_TEST_CONTENT_4)
                        .setStyle(new NotificationCompat.BigTextStyle()
                                .setBigContentTitle(PUSH_TEST_LARGE_TITLE_4)                // 펼쳐졌을 때 큰 텍스트 블록 제목
                                .setSummaryText(PUSH_TEST_LARGE_SUMMARY_4)                  // 펼쳐졌을 때 큰 텍스트 블록 요약
                                .bigText(PUSH_TEST_LARGE_CONTENT_4)                         // 펼쳐졌을 때 큰 텍스트 블록 내용
                        );
```

## 알림 업데이트

- 하나의 앱에 다수의 알림이 오는 경우 최신 알람 한개만 노출 가능.
- 알림 ID와 채널 ID는 다름.

- **<동일한 ID를 사용한 알림>**

``` java
// id값이 PUSH_TEST_ID_0인 푸시 알림 테스트
NotificationManager notificationManager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
notificationManager.notify(PUSH_TEST_ID_0, builder.build());

// id값이 PUSH_TEST_ID_0인 푸시 알림 테스트
NotificationManager notificationManager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
notificationManager.notify(PUSH_TEST_ID_0, builder.build());
```

- **<서로 다른 ID를 사용한 알림>**

``` java
// id값이 PUSH_TEST_ID_0인 푸시 알림 테스트
NotificationManager notificationManager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
notificationManager.notify(PUSH_TEST_ID_0, builder.build());

// id값이 PUSH_TEST_ID_1인 푸시 알림 테스트
NotificationManager notificationManager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
notificationManager.notify(PUSH_TEST_ID_1, builder.build());
```

## 알림 그룹

- 다수의 알림이 올 경우 알람을 모아보기 가능.

## 알림 채널

- 7.1 이하, 따로 설정하지 않으며 단일 채널로 운용.
- 8.0 이상부터, 다수 채널을 설정 가능하며 각 채널별로 알림 설정을 다르게 줄 수 있음.

``` java
private void createNotificationChannel() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            // [Android 8.0~] 채널 및 중요도 설정
            // 7.1 이하 버전은 단일 채널처럼 운용 됨.
            NotificationChannel channel = new NotificationChannel(CHANNEL_ID, CHANNEL_NAME,NotificationManager.IMPORTANCE_HIGH);
            channel.setDescription(CHANNEL_DESCRIPTION);
            NotificationManager notificationManager = getSystemService(NotificationManager.class);
            notificationManager.createNotificationChannel(channel);
        }
        // CHANNEL_NAME : "channel_name"
        // CHANNEL_DESCRIPTION : "channel_description"
    }
```

### 채널 제거

- 알림 생성 코드를 제거해도 한번 NotificationManager에 채널을 추가하면 재빌드 해도 채널이 남아있음.
- Android 8.0 이상에서, 채널을 모두 제거한 상태에서는 알림을 받을 수 없음.
- 알림 채널 제거 코드

``` java
private void deleteNotificationChannel() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            NotificationManager notificationManager = getSystemService(NotificationManager.class);
            notificationManager.deleteNotificationChannel(CHANNEL_ID);      // 채널 id로 삭제함에 주의
        }
    }
```

### 중요도

- 앱의 알림이 사용자를 방해하는 정도를 설정 가능.
- sdk 버전별로 구현이 다르므로 주의.
- **중요! NotificationChannel에 중요도를 설정하고 NotificationManager로 설정하면 코드를 변경해도 중요도가 변경되지 않음. (사용자는 앱 설정에서 변경 가능.)**

| 중요도 | 설명                              | 8.0 이상             | 7.1 이하                            |
| ------ | --------------------------------- | -------------------- | ----------------------------------- |
| 긴급   | 상태표시줄 & 알림음 & 헤즈업 알림 | `IMPORTANCE_HIGH`    | `PRIORITY_HIGH` 또는 `PRIORITY_MAX` |
| 높음   | 상태표시줄 & 알림음               | `IMPORTANCE_DEFAULT` | `PRIORITY_DEFAULT`                  |
| 중간   | 상태표시줄                        | `IMPORTANCE_LOW`     | `PRIORITY_LOW`                      |
| 낮음   | 알림 방해 없음                    | `IMPORTANCE_MIN`     | `PRIORITY_MIN`                      |

- 7.1 이하의 중요도 구현(Builder 에서 구현)

``` java
NotificationCompat.Builder builder = new NotificationCompat.Builder(this)
                                                                    .setPriority(NotificationCompat.PRIORITY_HIGH);
```

- 8.0 이상의 중요도 구현 (알림 채널에서 구현)

```java
NotificationChannel channel = new NotificationChannel(CHANNEL_ID, CHANNEL_NAME, 
NotificationManager.IMPORTANCE_MIN);
```

# 푸시 알림 서버

## FCM 설정

### Android 프로젝트에 Firebase 추가

- 사전 조건 : API 수준 16(Jelly Bean) 이상 타겟팅 / Gradle 4.1 이상 사용
- Firebase Console 로그인 후, 새 프로젝트 생성.
- 개요 화면에서 안드로이드 아이콘을 선택하여 설정 진행.
- 패키지 등록, google-service.json 추가, gradle 설정 진행.

### AndroidManifest 설정

- 인터넷 사용 권한 설정

``` xml
<uses-permission android:name="android.permission.INTERNET"/>
```

- FirebaseMessagingService를 상속받은 TestFirebaseMessagingService 를 AndroidManifest에 등록.

``` xml
<service
    android:name=".TestFirebaseMessagingService"
    android:exported="false">
    <intent-filter>
        <action android:name="com.google.firebase.MESSAGING_EVENT" />
    </intent-filter>
</service>
```

- (선택) 기본 알림 아이콘 및 색상 지정

``` xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_launcher_foreground" />
<meta-data
    android:name="com.google.firebase.messaging.default_notification_color"
    android:resource="@color/red" />
```

- (선택) 기본 알림 채널 Id 설정

``` xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_channel_id"
    android:value="@string/default_notification_channel_id" />
```

### FirebaseMessagingService 확장

- onNewToken(String token) : 토큰 등록시 호출되는 콜백 메소드.
- sdk 실행시 토큰이 생성되어 등록되며 다음의 상황에서 토큰은 갱신될 수 있음.
  `앱에서 인스턴스 ID 삭제` `새 기기에서 앱 복원` `사용자가 앱 삭제/재설치` `사용자가 앱 데이터 삭제`

``` java
// 새로운 토큰이 확인됬을 때 호출되는 메소드
@Override
public void onNewToken(@NonNull String s) {
    super.onNewToken(s);
    Log.e("테스트", "new Token : " + s);
    sendTokenToServer(s);       // 알림 서버가 있다면 토큰을 따로 등록시켜주는 부분
}
```

- onMessageReceived(RemoteMessage remoteMessage) : 메시지 수신시 호출되는 메소드.

``` java
// 메시지를 받았을 경우 실행되는 메소드
@Override
public void onMessageReceived(@NonNull RemoteMessage remoteMessage) {
    Log.e("테스트",
            "body : " + remoteMessage.getNotification().getBody()
                + "channel_id : " + remoteMessage.getNotification().getChannelId()
                + "title : " + remoteMessage.getNotification().getTitle());
    displayNotification(remoteMessage);             // 알림을 화면에 노출시키는 메소드
}
```

# 푸시 알림 테스트 방법

## FCM 콘솔에서 테스트 방법

### 토큰 얻기

다음과 같은 메소드를 호출하여 토큰값을 얻어냄.

``` java
private void registerTokenToFcm() {
        FirebaseInstanceId.getInstance().getInstanceId().addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
            @Override
            public void onComplete(@NonNull Task<InstanceIdResult> task) {
                if ( task.isSuccessful() == false) {
                    return;
                }

                String token = task.getResult().getToken();     // get Token
                Log.e("테스트", "토큰 : " + token);
            }
        });
    }
```

### 토큰을 FCM 콘솔에 추가

- `테스트` 버튼을 누르면 선택된 토큰에 대해 알림 전송.
- **주의** : `추가 옵션 > Android 알림 채널` 을 입력하지 않으면 채널 id 가 null 로 전송되어 제대로 알림을 수신하지 못할 수 있음.

# 참고

- Android 알림 만들기 공식 문서
  [https://developer.android.com/training/notify-user/build-notification](https://developer.android.com/training/notify-user/build-notification)
- Firebase Cloud Messaging 공식 가이드
  [https://firebase.google.com/docs/cloud-messaging/android/client](https://firebase.google.com/docs/cloud-messaging/android/client)
