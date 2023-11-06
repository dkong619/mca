AndroidManifest.xml

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
xmlns:tools="http://schemas.android.com/tools">
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission
android:name="android.permission.ACCESS_COARSE_LOCATION"/>
<uses-permission
android:name="android.permission.ACCESS_FINE_LOCATION"/>
<application
android:allowBackup="true"
android:dataExtractionRules="@xml/data_extraction_rules"
android:fullBackupContent="@xml/backup_rules"
android:icon="@mipmap/ic_launcher"
android:label="@string/app_name"
android:roundIcon="@mipmap/ic_launcher_round"
android:supportsRtl="true"
android:theme="@style/Theme.AndroidMap"
tools:targetApi="31">
<meta-data
android:name="com.google.android.geo.API_KEY"
android:value="AIzaSyDuWkGlIv4paYtnenJbhqtdl10Im0Wt16Q"/>
<activity
android:name=".MainActivity"
android:exported="true">
<intent-filter>
<action android:name="android.intent.action.MAIN" />
<category android:name="android.intent.category.LAUNCHER" />
</intent-filter>
</activity>
</application>
</manifest>


Activity_main.xml

<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
xmlns:app="http://schemas.android.com/apk/res-auto"
xmlns:tools="http://schemas.android.com/tools"
android:layout_width="match_parent"

android:layout_height="match_parent"
tools:context=".MainActivity">
<fragment
android:id="@+id/google_map"
android:layout_width="match_parent"
android:layout_height="match_parent"
android:name="com.google.android.gms.maps.SupportMapFragment"/>
</RelativeLayout>
y. MainActivity.xml
package com.example.androidmap;
import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;
import android.content.pm.PackageManager;
import android.location.Location;
import android.os.Bundle;
import android.view.WindowManager;
import android.widget.Toast;
import com.google.android.gms.location.FusedLocationProviderClient;
import com.google.android.gms.location.LocationServices;
import com.google.android.gms.maps.CameraUpdateFactory;
import com.google.android.gms.maps.GoogleMap;
import com.google.android.gms.maps.OnMapReadyCallback;
import com.google.android.gms.maps.SupportMapFragment;
import com.google.android.gms.maps.model.LatLng;
import com.google.android.gms.maps.model.MarkerOptions;
import com.google.android.gms.tasks.OnSuccessListener;
import com.google.android.gms.tasks.Task;
import com.karumi.dexter.Dexter;
import com.karumi.dexter.PermissionToken;
import com.karumi.dexter.listener.PermissionDeniedResponse;
import com.karumi.dexter.listener.PermissionGrantedResponse;
import com.karumi.dexter.listener.PermissionRequest;
import com.karumi.dexter.listener.single.PermissionListener;
public class MainActivity extends AppCompatActivity {
SupportMapFragment supportMapFragment;
FusedLocationProviderClient fusedLocationProviderClient;
@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
47
setContentView(R.layout.activity_main);
getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
WindowManager.LayoutParams.FLAG_FULLSCREEN);
supportMapFragment = (SupportMapFragment)
getSupportFragmentManager().findFragmentById(R.id.google_map);
fusedLocationProviderClient = (FusedLocationProviderClient)
LocationServices.getFusedLocationProviderClient(this);
Dexter.withContext(getApplicationContext()).withPermission(android.Manifest.
permission.ACCESS_FINE_LOCATION)
.withListener(new PermissionListener() {
@Override
public void onPermissionGranted(PermissionGrantedResponse
permissionGrantedResponse) {
getCurrentLocation();
}
@Override
public void onPermissionDenied(PermissionDeniedResponse
permissionDeniedResponse) {
}
@Override
public void
onPermissionRationaleShouldBeShown(PermissionRequest permissionRequest,
PermissionToken permissionToken) {
permissionToken.continuePermissionRequest();
}
}).check();
}
public void getCurrentLocation() {
if (ActivityCompat.checkSelfPermission(this,
android.Manifest.permission.ACCESS_FINE_LOCATION) !=
PackageManager.PERMISSION_GRANTED &&
ActivityCompat.checkSelfPermission(this,
android.Manifest.permission.ACCESS_COARSE_LOCATION) !=
PackageManager.PERMISSION_GRANTED) {
return;
}
Task<Location> task = fusedLocationProviderClient.getLastLocation();
task.addOnSuccessListener(new OnSuccessListener<Location>() {
@Override
48
public void onSuccess(Location location) {
supportMapFragment.getMapAsync(new OnMapReadyCallback() {
@Override
public void onMapReady(@NonNull GoogleMap googleMap) {
if(location!=null){
LatLng latLng = new LatLng(location.getLatitude(),
location.getLongitude());
MarkerOptions markerOptions = new
MarkerOptions().position(latLng).title("Current Location");
googleMap.addMarker(markerOptions);
googleMap.animateCamera(CameraUpdateFactory.newLatLngZoom(latLng,15))
;
}
else{
Toast.makeText(MainActivity.this, "Please turn your location
permissions on", Toast.LENGTH_SHORT).show();
}
}
});
}
});
}
}
