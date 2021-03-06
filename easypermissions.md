#EasyPermissions

Even thought this tool had make the task more easier, but you still have to figure out the correct situation to run your code:

* when all permissions granted
* when not all permissions granted, click events on the dialog invoked from method `EasyPermissions.requestPermissions`
* when not all permissions granted, click events on `AppSettingsDialog`


### PREREQUISITE
setup gradle
```java
comple 'pub.devrel:easypermissions:0.3.0'
```
### Add to lifecycle onCreate or onResume of your View \(in MVP, Activity or Fragment\) 

```java
        String[] perms = {
                Manifest.permission.ACCESS_COARSE_LOCATION,
                Manifest.permission.ACCESS_FINE_LOCATION,
                Manifest.permission.READ_EXTERNAL_STORAGE,
                Manifest.permission.WRITE_EXTERNAL_STORAGE
        };
        if (!EasyPermissions.hasPermissions(this, perms)) {
            EasyPermissions.requestPermissions(this, getString(R.string.perm_more), 1, perms);
        } else {

            // your code on UiThread
        }
```

* you need to set parameter `perms`, which indicates the permissions your app need to require.

* you need to put your custom notice in the 2nd parameter of the function `requestPermissions`, which stands for the rationale for app to display for users to get permissions.
* 3rd parameter of the function `requestPermissions` is request code, you can customize it by yourself as well.

### Override the method onRequestPermissionResult in Activity

```java
    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        EasyPermissions.onRequestPermissionsResult(requestCode, permissions, grantResults, this);
    }
```

###implements EasyPermissions.PermissionCallbacks
```java
public class MainActivity extends AppCompatActivity implements EasyPermissions.PermissionCallbacks {
    ...
}
```

###Override method onPermissionsDenied
```java
    @Override
    public void onPermissionsGranted(int requestCode, List<String> perms) {
        // this method will be invoked when user click the positive button on EasyPermissions dialog, 
        // make sure to continue your task only when your get all the permissions that you wanted
        if (EasyPermissions.hasPermissions(this, this.perms)){
            // your code on UiThread
        }
    }

    @Override
    public void onPermissionsDenied(int requestCode, List<String> perms) {
        // force user to give permissions
        new AppSettingsDialog.Builder(this).setRationale(getString(R.string.perm_rationale)).setNegativeButton(R.string.perm_leave).build().show();
    }
```
* Display the rationale to warn user if he/she don't grant permissions then the app will be shutdown(see next step)

###shutdown the app if user is not easy-going
```java
    @Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        if (requestCode == AppSettingsDialog.DEFAULT_SETTINGS_REQ_CODE) {
            
            if (!EasyPermissions.hasPermissions(this, perms)){
                // when user click negative button on the dialog we just warned, shutdown the app without any hesitation and mercy.
                finish();
            }else{
                // your code on UiThread
            }

        }
    }

```


