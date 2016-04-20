##Escuela Colombiana de Ingeniería
###Construcción de Software - COSW
###Introducción a Android

En este ejercicio usted va a implementar una aplicación que informa la posición geográfica de quien la usa.


###Parte I. Layouts, uso y acceso a componentes visuales (vistas).
1. Cree un nuevo proyecto en Android Studio, y seleccione la creación de una Actividad 'en blanco'.

2. Agregue los siguientes permisos en el manifiesto de Android, de manera que la aplicación pueda acceder a los servidios de geoposicionamiento del dispositivo:

	```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.LOCATION_HARDWARE" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

1. Haga que la Actividad principal implemente un 'listener' de georeferenciación (haga que implemente la interfaz Locationlistener). Para registrar a la actividad principal como la encargada de manejar los eventos de cambio  en la posición, agregue lo siguiente en su método 'onCreate':
	
	
	```java	
LocationManager locationManager = (LocationManager)getSystemService(Context.LOCATION_SERVICE);  

    locationManager.requestLocationUpdates( LocationManager.GPS_PROVIDER,
                1,   // periodo en segundos de actualización
                0, //cambio mínimo en metros para actualizar
                this);
```


3. En el layout de la actividad principal, agregue dos vistas de tipo TextView. Tenga presente qué identificador les asigna, para su posterior uso.

4. Ajuste el método _onLocationChanged(Location location)_ para que cuando se de el evento, obtenga del objeto 'location' la nueva posición del dispositivo, y ponga la latitud y longitud en las dos vistas creadas anteriormente. Recuerde que para acceder a la vista, puede usar el método 'findViewById' disponible para cualquier actividad, y que dicho método retorna un objeto de tipo 'View', por lo que tendrá que hacer _downcasting_ si lo quiere asignar a una variable de un tipo concreto:

	```java	
		this.findViewById(R.id.ID_DE_LA_VISTA);
```

4. Pruebe el funcionamiento de la aplicación.


###Parte II. Eventos e Intenciones (Intents)

Como la actividad principal creada por defecto es de tipo ActionBarActivity, al ejecutar la aplicación usted observará que la misma cuenta con un elemento de menú desplegable:

![](img/activitybar.png)

Va a agregar las siguientes opciones al menú: 'Acerca del autor' y 'Contactar al autor'.
	
1. Para agregar opciones al menú de la actividad principal, abra la sección de menús dentro de la sección de recursos, y en ésta edite el XML que corresponda a la actividad principal:

	![](img/menuxml.png)

2. Agregue las cadenas que utilizará como etiquetas en las dos nuevas opciones de menú que va a crear. Para esto edite el XML res/values/strings.xml y agregue las entradas requeridas. Por ejemplo:

	```xml
<string name="send_mail_label">Contactar al autor</string>
```

3. Para agregar un nuevo elemento de menú, simplemente agregue un nuevo elemento <item> dentro del XML, teniendo cuidado en asignar un ID único (en este ejemplo, se asigna "@+id/sendemail") y que en el título haga referencua a la cadena definida anteriormente (agregando '@string/' como prefijo del nombre asignado a la cadena):

	```xml
    <item android:id="@+id/sendemail" android:title="@string/send_mail_label"
        android:orderInCategory="100" app:showAsAction="never"  />
```

4. Para definir qué se hará cuando se seleccione cada opción de menú, ajuste el método _onOptionsItemSelected_ de la Actividad principal, y ajuste los condicionales de acuerdo con los identificadores dados a los 'items' del menú:

	```java
		if (id == R.id.sendemail) {
				//acción a realizar
			
            return true;
        }
        else if (id==R.id.IDENTIFICADORASIGNADO){
				//acción a realizar
			
            return true;							
				
        }
```

5. Para la opción de 'Acerca del autor', use el componente AlertDialog para mostrar su nombre:

	```java

            AlertDialog.Builder alertBuilder=new AlertDialog.Builder(this);
            alertBuilder.setPositiveButton("Ok", new DialogInterface.OnClickListener() {
                public void onClick(DialogInterface dialog, int id) {
	
                }
            });
            AlertDialog alertDialog = alertBuilder.create();
            alertDialog.setTitle("Acerca de...");
            alertDialog.setMessage("Sobre la aplicación bla bla bla");

            alertDialog.show();
```

6. Para la opción de 'Contactar al autor', envíe una Intención (Intent) de tipo 'ACTION_SENDTO' al sistema operativo, enviando como 'extras' los detalles del mensaje (haga los ajustes para que el mensaje vaya dirigido a su correo):

	```java
            Intent emailIntent = new Intent(Intent.ACTION_SENDTO, Uri.fromParts("mailto", "abc@gmail.com", null));
            emailIntent.putExtra(Intent.EXTRA_SUBJECT, "Subject");
            emailIntent.putExtra(Intent.EXTRA_TEXT, "Body");            
            startActivity(Intent.createChooser(emailIntent, "Send email..."));
        
 ```

7. Verifique el funcionamiento de la aplicación.

###Parte III. Cambio e interacción entre Actividades.

Ahora, va a agregar una funcionalidad que le permite visualizar un mapa que le indica -a partir de las coordenadas capturadas por la actividad principal-, en qué parte del mundo se encuentra.

1. Cree una nueva actividad de tipo 'Google Maps':
	![](img/newgoogleact.png)

2. Abra el archivo generado en res/values/google_maps_api.xml. En este archivo se debe incluír una llave de desarrollador para poder hacer uso del API de google maps. Siga las instrucciones dadas en el TODO (dentro de este mismo archivo) para obtener su propia llave, de manera que la configuración quede con algo como:


	```xml
    <string name="google_maps_key" translatable="false" templateMergeStrategy="preserve">
       AIzaSyDhbE0FFYgFcWS1wu3vZSVjafVDGFmBAKw
    </string>
```

3. Modfique el menú de la actividad principal para que ahora tenga la opción 'Visualizar mi ubicación'. Cuando se seleccione dicha opción, se debe crear un Intent explícito (asociado a una actividad en concreto), y enviándo como 'extras' los valores de latitud y longitud que en ese momento tenga la actividad principal. Por ejemplo, para enviar una Intención directamente a la actividad definida en la clase 'ClaseDeLaActividad', y enviar un parámetro asociado al nombre 'param1':

	```java
         Intent i=new Intent(this,ClaseDeLaActividad.class);
         i.putExtra("param1",1);
         startActivity(i);

 ```
 
4. Modifique la actividad creada para el mapa, y en el 'setupMap' haga que se lea la latitud y la longitud enviados como 'extras' por la actividad principal, y los en lugar de las coordenadas usadas por defecto (latitud=0, longitud=0). Una actividad puede consultar la Intención con la cual fue activada en cualquer punto, simplemente invocando 'getIntent()'. Por ejemplo, si la actividad 'ClaseDeLaActividad' usada como ejemplo quisiera conocer el valor de 'param1':

	```java
	Intent i=getIntent();
	int valor=i.getIntExtra("param1");
 ```

5. Verifique el funcionamiento de su aplicación. Opcionalmente revise el [API de la clase GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#nested-class-summary) para afinar el comportamiento del mapa (agregar elementos geométricos, cambiar el zoom, etc).
