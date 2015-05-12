# navutils
Android Navigation Utils examples


Define PARENT_ACTIVITY in manifest as described [here](http://developer.android.com/training/implementing-navigation/ancestral.html)

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
        intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    }
    return intent;
}
```


