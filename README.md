# Retainza Android SDK

Identify users, track events, receive push and show in-app messages from
[Retainza](https://retainza.com).

## Requirements

- Android 7.0+ (API 24)
- Kotlin coroutines (the SDK's calls are `suspend` functions)
- To receive push, add Firebase Messaging to your app and forward its callbacks
  (see below). The SDK does not pull in Firebase itself.

## Install

The SDK is on Maven Central, which new Android projects already include. In
your app module's `build.gradle.kts`:

```kotlin
dependencies {
    implementation("com.retainza:retainza-android:1.0.0")
}
```

## Quick start

```kotlin
import com.retainza.sdk.Retainza
import com.retainza.sdk.RetainzaConfig
import com.retainza.sdk.attr

// Application.onCreate
Retainza.start(this, RetainzaConfig(
    publicKey = "pk_test_…",           // pk_live_… in release builds
    userProvider = { AuthStore.userId },
))

// After sign-in (from a coroutine)
Retainza.client?.identify("user-123", mapOf("plan" to "pro".attr))

// Anywhere
Retainza.client?.track("order_placed", mapOf("total" to 42.5.attr))

// On sign-out
Retainza.client?.reset()
```

### Push

Android allows one `FirebaseMessagingService`, so forward two calls from yours:

```kotlin
class MyMessagingService : FirebaseMessagingService() {
    override fun onNewToken(token: String) {
        RetainzaPush.handleNewToken(token)
    }

    override fun onMessageReceived(message: RemoteMessage) {
        if (RetainzaPush.handleMessageReceived(message.data)) return
        // …your own push handling
    }
}
```

Get your public key from the Retainza dashboard under **API keys**; the full
integration guide is in the dashboard under **Developer docs**.

## Support

Questions or issues: [hello@retainza.com](mailto:hello@retainza.com).

## License

MIT. See [LICENSE](LICENSE).
