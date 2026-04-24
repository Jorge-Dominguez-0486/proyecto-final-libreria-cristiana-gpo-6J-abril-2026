Hola Jorge Luis, es un gusto saludarte. Vamos a estructurar este ecosistema de desarrollo de manera lógica y profesional. Tu visión de integrar habilidades de agente con una arquitectura sólida en Flutter es ambiciosa y muy potente.

A continuación, detallo la estructura de la habilidad global `.agents`, la configuración del entorno y el desarrollo inicial del proyecto **proyectolibreria** (enfocado en el CRUD de libros/mascotas).

---

## 1. Estructura de la Habilidad Agente Global: `.agents`

Esta carpeta servirá como la "mente" de tu automatización, separando la lógica de diseño, código y scraping.

```text
.agents/
├── SKILL.md              # Documentación técnica y reglas del agente
├── scripts/              # Automatizaciones (bash, python o dart) para tareas repetitivas
├── ejemplos/             # Snippets de código de referencia y UI patterns
└── resources/            # Assets, logos, y archivos de configuración base
```

### Contenido de `SKILL.md` (Extracto)
> **Propósito:** Actuar como un arquitecto experto en Flutter y Firebase.
> **Habilidades:**
> * **Diseño:** Implementación de Material Design 3.
> * **Código:** Generación de lógica de negocio limpia (Clean Architecture).
> * **Scraping:** Extracción de datos para alimentar catálogos de libros/mascotas.

---

## 2. Configuración del Entorno y Prerrequisitos

Antes de codificar, debemos asegurar que el "motor" esté encendido.

### Verificación de Instalaciones
Ejecuta estos comandos en tu terminal (VS Code o Antigravity):

1.  **Flutter:** `flutter --version` (Si no está, descarga el SDK oficial).
2.  **Firebase CLI:** `firebase --version`
    * *Si no está instalado:* `npm install -g firebase-tools`
3.  **Flutterfire CLI:** `dart pub global activate flutterfire_cli`

### Conectividad Firebase
1.  **Login:** `firebase login` (Sigue el flujo en el navegador).
2.  **Configurar Proyecto:**
    * Crea el proyecto en [Firebase Console](https://console.firebase.google.com/).
    * Habilita **Firestore Database** en modo prueba.
    * En la terminal del proyecto: `flutterfire configure` (Selecciona tu ID de proyecto y la plataforma Android).

---

## 3. Creación del Proyecto: `proyectolibreria`

Iniciamos la creación del proyecto y la estructura de carpetas funcional.

```bash
flutter create proyectolibreria
cd proyectolibreria
```

### Dependencias en `pubspec.yaml`
Añade esto bajo `dependencies`:
```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.x.x
  cloud_firestore: ^4.x.x
  cupertino_icons: ^1.0.2
```
*Ejecuta `flutter pub get` después de guardar.*

---

## 4. Estructura de Archivos y Código CRUD

Implementaremos una estructura organizada para el CRUD de mascotas/libros.

### Estructura de carpetas:
```text
lib/
├── main.dart
├── models/
│   └── mascota_model.dart
├── services/
│   └── firebase_service.dart
└── screens/
    ├── home_screen.dart
    └── crud_screen.dart
```

### A. Modelo de Datos (`lib/models/mascota_model.dart`)
```dart
class Mascota {
  String id;
  String nombre;
  String especie;

  Mascota({required this.id, required this.nombre, required this.especie});

  Map<String, dynamic> toMap() => {'nombre': nombre, 'especie': especie};
}
```

### B. Servicio Firestore (`lib/services/firebase_service.dart`)
```dart
import 'package:cloud_firestore/cloud_firestore.dart';

FirebaseFirestore db = FirebaseFirestore.instance;

// CREATE
Future<void> addMascota(String nombre, String especie) async {
  await db.collection('mascotas').add({'nombre': nombre, 'especie': especie});
}

// READ
Stream<QuerySnapshot> getMascotas() {
  return db.collection('mascotas').snapshots();
}

// UPDATE
Future<void> updateMascota(String id, String nuevoNombre) async {
  await db.collection('mascotas').doc(id).update({'nombre': nuevoNombre});
}

// DELETE
Future<void> deleteMascota(String id) async {
  await db.collection('mascotas').doc(id).delete();
}
```

### C. UI Principal (`lib/screens/home_screen.dart`)
```dart
import 'package:flutter/material.dart';
import '../services/firebase_service.dart';

class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Veterinaria - Libros y Mascotas')),
      body: StreamBuilder(
        stream: getMascotas(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return const Center(child: CircularProgressIndicator());
          var docs = snapshot.data!.docs;
          return ListView.builder(
            itemCount: docs.length,
            itemBuilder: (context, i) {
              return ListTile(
                title: Text(docs[i]['nombre']),
                subtitle: Text(docs[i]['especie']),
                trailing: IconButton(
                  icon: const Icon(Icons.delete, color: Colors.red),
                  onPressed: () => deleteMascota(docs[i].id),
                ),
                onTap: () {
                  // Aquí se dispararía la edición (Update)
                  updateMascota(docs[i].id, "${docs[i]['nombre']} (Editado)");
                },
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => addMascota("Nueva Mascota", "Canino"),
        child: const Icon(Icons.add),
      ),
    );
  }
}
```

### D. Punto de Entrada (`lib/main.dart`)
```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'firebase_options.dart'; // Generado por flutterfire configure
import 'screens/home_screen.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(options: DefaultFirebaseOptions.currentPlatform);
  runApp(const VeterinariaApp());
}

class VeterinariaApp extends StatelessWidget {
  const VeterinariaApp({super.key});
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      theme: ThemeData(useMaterial3: true, colorSchemeSeed: Colors.green),
      home: HomeScreen(),
    );
  }
}
```

---

## 5. Pruebas de Verificación
1.  **Terminal:** Ejecuta `flutter doctor` para asegurar que el IDE y el SDK no tengan errores.
2.  **Consola Firebase:** Verifica que al presionar el botón `+` en la app, los datos aparezcan en tiempo real en la pestaña de Firestore.
3.  **Hot Reload:** Usa la tecla `r` en la terminal o el rayo en VS Code para ver cambios en la UI instantáneamente.

¿Deseas que profundicemos en el desarrollo de la habilidad de **scraping** dentro de la carpeta `.agents` para extraer datos de libros automáticamente?
