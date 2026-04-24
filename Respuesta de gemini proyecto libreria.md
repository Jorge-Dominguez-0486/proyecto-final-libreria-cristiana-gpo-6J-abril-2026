Hola **Jorge Luis Dominguez Ortiz**. Tienes toda la razón, mantengamos el enfoque estrictamente en la **Librería Cristiana**. Vamos a construir la estructura del agente `.agents` y el proyecto Flutter `proyectolibreria` con una secuencia lógica y profesional.

---

## 1. Estructura de la Habilidad Agente Global: `.agents`

Esta carpeta centraliza la inteligencia de tu automatización para la librería.

```text
.agents/
├── SKILL.md              # Documentación y reglas del agente (Diseño, Código, Scraping)
├── scripts/              # Automatización para carga de libros cristianos (CSV a Firestore)
├── ejemplos/             # Plantillas de UI para libros y devocionales
└── resources/            # Logos cristianos, iconos de biblias y paleta de colores
```

### Contenido de `SKILL.md`
> **Agente de Librería Cristiana**
> * **Skill de Diseño:** Implementación de temas sobrios, uso de fuentes legibles para lectura teológica.
> * **Skill de Código:** Generación de Clean Architecture en Dart y servicios de Firebase.
> * **Skill de Scraping:** Extracción de metadatos de biblias y libros cristianos de catálogos públicos.

---

## 2. Prerrequisitos y Configuración del Entorno

### Instalación de Herramientas
Si aún no tienes las herramientas, ejecuta esto en tu terminal:

1.  **Flutter:** Descárgalo y verifica con `flutter doctor`.
2.  **Firebase CLI:** * Instalación: `npm install -g firebase-tools`
    * Login: `firebase login`
3.  **Flutterfire CLI:**
    * Instalación: `dart pub global activate flutterfire_cli`

### Conectividad con Console Firebase
1.  Crea tu proyecto en [Firebase Console](https://console.firebase.google.com/) llamado **"LibreriaCristiana"**.
2.  Habilita **Cloud Firestore** en modo de prueba.
3.  En la terminal, dentro de tu carpeta de proyecto:
    `flutterfire configure` (Selecciona tu proyecto y la plataforma Android).

---

## 3. Proyecto Flutter: `proyectolibreria`

### Configuración de `pubspec.yaml`
Añade las dependencias necesarias:

```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.0.0
  cloud_firestore: ^4.0.0
  cupertino_icons: ^1.0.2
```
*Ejecuta `flutter pub get`.*

---

## 4. Código del Proyecto (CRUD de Libros Cristianos)

### A. Modelo de Datos (`lib/models/libro_model.dart`)
```dart
class Libro {
  String id;
  String titulo;
  String autor;
  String categoria;

  Libro({required this.id, required this.titulo, required this.autor, required this.categoria});

  Map<String, dynamic> toMap() {
    return {
      'titulo': titulo,
      'autor': autor,
      'categoria': categoria,
    };
  }
}
```

### B. Servicio de Firebase (`lib/services/firebase_service.dart`)
```dart
import 'package:cloud_firestore/cloud_firestore.dart';

FirebaseFirestore db = FirebaseFirestore.instance;

// CREATE
Future<void> addLibro(String titulo, String autor, String categoria) async {
  await db.collection('libreria').add({
    'titulo': titulo,
    'autor': autor,
    'categoria': categoria,
  });
}

// READ
Stream<QuerySnapshot> getLibros() {
  return db.collection('libreria').snapshots();
}

// UPDATE
Future<void> updateLibro(String id, String nuevoTitulo) async {
  await db.collection('libreria').doc(id).update({'titulo': nuevoTitulo});
}

// DELETE
Future<void> deleteLibro(String id) async {
  await db.collection('libreria').doc(id).delete();
}
```

### C. Pantalla Principal UI (`lib/screens/home_screen.dart`)
```dart
import 'package:flutter/material.dart';
import '../services/firebase_service.dart';

class HomeScreen extends StatelessWidget {
  const HomeScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Librería Cristiana - Inventario'),
        backgroundColor: Colors.brown[700],
        foregroundColor: Colors.white,
      ),
      body: StreamBuilder(
        stream: getLibros(),
        builder: (context, AsyncSnapshot<QuerySnapshot> snapshot) {
          if (!snapshot.hasData) return const Center(child: CircularProgressIndicator());
          
          return ListView.builder(
            itemCount: snapshot.data!.docs.length,
            itemBuilder: (context, index) {
              var doc = snapshot.data!.docs[index];
              return ListTile(
                leading: const Icon(Icons.book_online),
                title: Text(doc['titulo']),
                subtitle: Text("${doc['autor']} - ${doc['categoria']}"),
                trailing: IconButton(
                  icon: const Icon(Icons.delete, color: Colors.red),
                  onPressed: () => deleteLibro(doc.id),
                ),
                onTap: () => updateLibro(doc.id, "${doc['titulo']} (Actualizado)"),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        backgroundColor: Colors.brown[700],
        child: const Icon(Icons.add, color: Colors.white),
        onPressed: () => addLibro("Biblia de Estudio", "Reina Valera", "Biblias"),
      ),
    );
  }
}
```

### D. Archivo Principal (`lib/main.dart`)
```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'firebase_options.dart';
import 'screens/home_screen.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(options: DefaultFirebaseOptions.currentPlatform);
  runApp(const MaterialApp(
    debugShowCheckedModeBanner: false,
    home: HomeScreen(),
  ));
}
```

---

## 5. Verificación de Funcionalidad
1.  **Terminal:** Ejecuta `flutter run` para iniciar en tu emulador o dispositivo Android.
2.  **Consola Firebase:** Abre la pestaña de **Firestore** y observa cómo se crean los documentos en la colección `libreria` al presionar el botón de agregar.
3.  **Configuración IDE:** Asegúrate de que en **VS Code** o **Antigravity** tengas instaladas las extensiones de "Dart" y "Flutter" para obtener autocompletado y depuración.

Con esta estructura, tu agente `.agents` ya tiene un proyecto de **Librería Cristiana** real sobre el cual trabajar sus habilidades de scraping y diseño.
