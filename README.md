# flutterfood
import 'package:flutter/material.dart';
import 'package:qr_flutter/qr_flutter.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Food Order & QR Payment',
      theme: ThemeData(primarySwatch: Colors.blue),
      home: FoodOrderScreen(),
    );
  }
}

class FoodOrderScreen extends StatefulWidget {
  @override
  _FoodOrderScreenState createState() => _FoodOrderScreenState();
}

class _FoodOrderScreenState extends State<FoodOrderScreen> {
  List<Map<String, dynamic>> menu = [
    {'name': 'Cơm', 'price': 30000, 'options': ['Thêm thịt', 'Thêm hành']},
    {'name': 'Phở', 'price': 35000, 'options': ['Bò thêm', 'Không giá']},
    {'name': 'Bún', 'price': 32000, 'options': ['Thêm chả', 'Thêm rau']},
    {'name': 'Cháo', 'price': 28000, 'options': ['Thêm trứng', 'Không hành']},
    {'name': 'Cà phê', 'price': 25000, 'options': ['Đường ít', 'Sữa nhiều']},
  ];

  List<Map<String, dynamic>> order = [];
  
  void addToOrder(Map<String, dynamic> item, String option) {
    setState(() {
      order.add({'name': item['name'], 'price': item['price'], 'option': option});
    });
  }
  
  double getTotalPrice() {
    return order.fold(0, (sum, item) => sum + item['price']);
  }
  
  void showQRCode() {
    String total = getTotalPrice().toStringAsFixed(0);
    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: Text('QR Code Thanh Toán'),
        content: QrImage(
          data: 'https://payment.example.com?amount=$total',
          size: 200,
        ),
      ),
    );
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Gọi món & Thanh toán QR')),
      body: Column(
        children: [
          Expanded(
            child: ListView(
              children: menu.map((item) {
                return ExpansionTile(
                  title: Text('${item['name']} - ${item['price']} VND'),
                  children: item['options'].map<Widget>((option) {
                    return ListTile(
                      title: Text(option),
                      trailing: Icon(Icons.add),
                      onTap: () => addToOrder(item, option),
                    );
                  }).toList(),
                );
              }).toList(),
            ),
          ),
          Divider(),
          Text('Tổng tiền: ${getTotalPrice()} VND', style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold)),
          ElevatedButton(
            onPressed: showQRCode,
            child: Text('Tạo mã QR thanh toán'),
          ),
        ],
      ),
    );
  }
}
