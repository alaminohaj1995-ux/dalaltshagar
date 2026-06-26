// مثال لنموذج البيانات في Dart
class Listing {
  final String id;
  final String title;
  final double price;
  final String category; // 'car', 'land', 'house_rent'
  final String imageUrl;
  final String description;

  Listing({required this.id, required this.title, required this.price, required this.category, required this.imageUrl, required this.description});

  // تحويل البيانات من Firebase إلى Object
  factory Listing.fromMap(Map<String, dynamic> map, String id) {
    return Listing(
      id: id,
      title: map['title'],
      price: map['price'].toDouble(),
      category: map['category'],
      imageUrl: map['imageUrl'],
      description: map['description'],
    );
  }
}
import 'package:cloud_firestore/cloud_firestore.dart';

class DatabaseService {
  final CollectionReference listings = FirebaseFirestore.instance.collection('listings');

  // دالة لجلب البيانات حسب النوع (سيارات، أراضي، إيجار)
  Stream<List<Listing>> getListings(String category) {
    return listings
        .where('category', isEqualTo: category)
        .snapshots()
        .map((snapshot) => snapshot.docs
            .map((doc) => Listing.fromMap(doc.data() as Map<String, dynamic>, doc.id))
            .toList());
  }
}
