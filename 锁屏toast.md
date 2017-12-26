```java
final WindowManager.LayoutParams params = toast.getWindowParams();
        params.flags |= WindowManager.LayoutParams.FLAG_SHOW_WHEN_LOCKED;
        params.flags |= WindowManager.LayoutParams.FLAG_TURN_SCREEN_ON;
        toast.show();
```
