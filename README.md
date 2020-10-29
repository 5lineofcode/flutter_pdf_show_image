```
import 'dart:io';
import 'dart:typed_data';

import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'package:open_file/open_file.dart';
import 'package:path_provider/path_provider.dart';
import 'package:pdf/pdf.dart';
import 'package:pdf/widgets.dart' as pw;

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);

  final String title;

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  @override
  void initState() {
    super.initState();
    initData();
  }

  PdfImage pdfImage;
  Uint8List imageBit;
  initData() async {
    var data = await rootBundle.load('asset/img/dbz.jpg');
    imageBit = data.buffer.asUint8List();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Container(),
      floatingActionButton: FloatingActionButton(
        onPressed: () async {
          final doc = pw.Document();

          doc.addPage(
            pw.Page(
              build: (pw.Context context) {
                return pw.Row(
                  children: [
                    pw.Text(
                      "Hello World",
                      style: pw.TextStyle(
                        fontSize: 30.0,
                        fontWeight: pw.FontWeight.bold,
                      ),
                    ),
                    pw.Image(PdfImage.file(
                      doc.document,
                      bytes: imageBit,
                    )),
                  ],
                );
              },
            ),
          );

          final dir = await getExternalStorageDirectory();
          final file = File('${dir.path}/example.pdf');
          file.writeAsBytesSync(doc.save());

          OpenFile.open(file.path);
        },
        tooltip: 'Increment',
        child: Text("Create Pdf"),
      ),
    );
  }
}

```
