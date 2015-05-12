# navutils
Android Navigation Utils examples

todo: Make a library that allows full dynamic control for custom intents

## Android System way using manifest tags

### Web Intents

Define PARENT_ACTIVITY in manifest as described [here](http://developer.android.com/training/implementing-navigation/ancestral.html)

Web Intent to test with
https://applinktest.appspot.com/app-link.html?url=android-app%3A%2F%2Fca.nickadams.navutils%2Fexample%2Ftest

```
<activity
    android:name=".ChildActivity"
    android:label="@string/title_activity_child"
    android:parentActivityName=".MainActivity" >
    <meta-data
        android:name="android.support.PARENT_ACTIVITY"
        android:value="ca.nickadams.navutils.MainActivity" />

    <intent-filter android:label="view child">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <!-- Accepts URIs that begin with "example://test" -->
        <data android:scheme="example"
            android:host="test" />
    </intent-filter>
</activity>
```

Then Override getSupportParentActivityIntent to change the intent

```
@Nullable
@Override
public Intent getSupportParentActivityIntent() {
    Intent intent = super.getSupportParentActivityIntent();
    if (intent != null) {
        // add clear top flag so it doesn't create new instance of parent.
        intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    }
    return intent;
}
```

The Parent activity could also be defined as SingleTop and then you wouldn't need to clear top
as it would be done automatically.

Note:
Parent navigation does not work when starting from adb ex:
`adb shell am start -a android.intent.action.VIEW -d "example://test" ca.nickadams.navutils`


### Push Notifications

Intent for push notification handling
taken from guide [here](http://developer.android.com/guide/topics/ui/notifiers/notifications.html)

```
Intent activityIntent = new Intent(this, ChildActivity.class);

TaskStackBuilder stackBuilder = TaskStackBuilder.create(this);
// Adds the back stack for the Intent (but not the Intent itself)
stackBuilder.addParentStack(ChildActivity.class);
// Adds the Intent that starts the Activity to the top of the stack
stackBuilder.addNextIntent(activityIntent);

PendingIntent pendingIntent = stackBuilder.getPendingIntent(0, PendingIntent.FLAG_CANCEL_CURRENT);
```

### How it all works

We could use `finish()` instead of `NavUtils.NavigateUpTo(Activity sourceActivity, Intent upIntent)` but
that would require us to be 100% certain that the parent activity is already running. If possible
it is advised to use the default impl.

The [TaskStackBuilder](http://developer.android.com/reference/android/support/v4/app/TaskStackBuilder.html) does
`context.startActivities()` which allows us to be able to build the correct parent stack. We could build
these manually if we desired instead of using the ancestral parent navigation.

For Activities that can have multiple parents a default should be implemented as the parent in the manifest and it can be overridden in the
Activity as required.