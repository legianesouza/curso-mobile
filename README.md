import 'package:flutter/material.dart';

void main() => runApp(const MyApp());

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: ProductListScreen(),
    );
  }
}

class Product {
  final String name, description, category, imageUrl;
  final double purchasePrice, salePrice, discount;
  final int quantity;
  final bool isActive, isOnSale;

  const Product({
    required this.name,
    required this.purchasePrice,
    required this.salePrice,
    required this.quantity,
    required this.description,
    required this.category,
    required this.imageUrl,
    required this.isActive,
    required this.isOnSale,
    required this.discount,
  });
}

class ProductListScreen extends StatefulWidget {
  @override
  State<ProductListScreen> createState() => _ProductListScreenState();
}

class _ProductListScreenState extends State<ProductListScreen> {
  final List<Product> products = [
    Product(
      name: "Vestido outono/inverno",
      purchasePrice: 49.9,
      salePrice: 159.9,
      quantity: 15,
      description: "Vestido de malha feminino moda outono/inverno",
      category: "Vestidos",
      imageUrl: "https://images.pexels.com/photos/20099739/pexels-photo-20099739/free-photo-of-mulher-modelo-arvore-de-pe.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1",
      isActive: true,
      isOnSale: false,
      discount: 0,
    ),
    Product(
      name: "Moda praia com 60% de desconto",
      purchasePrice: 25,
      salePrice: 39.99,
      quantity: 13,
      description: "Promoção IMPERDÍVEL de moda praia!\nParcelamos em até 6x sem juros",
      category: "Promoção",
      imageUrl: "https://media.istockphoto.com/id/2154294202/pt/foto/happy-young-woman-with-red-hair-running-with-yellow-scarf-at-beach.jpg?s=612x612&w=0&k=20&c=G-jKwF5ablVQgyv8irMxPbZ3u_BDUTUHpTm__o7m3tM=",
      isActive: true,
      isOnSale: true,
      discount: 60,
    ),
  ];

  void _navigateToAddProduct() async {
    final newProduct = await Navigator.push<Product>(
      context,
      MaterialPageRoute(builder: (_) => ProductFormScreen()),
    );
    if (newProduct != null) setState(() => products.add(newProduct));
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Produtos (${products.length})"),
        actions: [IconButton(icon: const Icon(Icons.add), onPressed: _navigateToAddProduct)],
      ),
      body: ListView.builder(
        itemCount: products.length,
        itemBuilder: (_, i) {
          final p = products[i];
          return ListTile(
            leading: Image.network(p.imageUrl, width: 50, height: 50, errorBuilder: (_, __, ___) => const Icon(Icons.error)),
            title: Text(p.name),
            subtitle: Text("R\$${p.salePrice.toStringAsFixed(2)}${p.isOnSale ? " (${p.discount}% OFF)" : ""}"),
            onTap: () => Navigator.push(context, MaterialPageRoute(builder: (_) => ProductDetailScreen(product: p))),
          );
        },
      ),
    );
  }
}

class ProductFormScreen extends StatefulWidget {
  @override
  State<ProductFormScreen> createState() => _ProductFormScreenState();
}

class _ProductFormScreenState extends State<ProductFormScreen> {
  final _formKey = GlobalKey<FormState>();
  final Map<String, TextEditingController> _controllers = {
    "name": TextEditingController(),
    "purchasePrice": TextEditingController(),
    "salePrice": TextEditingController(),
    "quantity": TextEditingController(),
    "description": TextEditingController(),
    "category": TextEditingController(),
    "imageUrl": TextEditingController(),
  };

  bool _isActive = true, _isOnSale = false;
  double _discount = 0;

  Widget _buildTextField(String key, String label, {int maxLines = 1, bool isNumber = false}) {
    return TextFormField(
      controller: _controllers[key],
      decoration: InputDecoration(labelText: label),
      keyboardType: isNumber ? TextInputType.number : null,
      maxLines: maxLines,
      validator: (v) => v == null || v.isEmpty ? "Obrigatório" : null,
    );
  }

  void _saveProduct() {
    if (_formKey.currentState!.validate()) {
      Navigator.pop(
        context,
        Product(
          name: _controllers["name"]!.text,
          purchasePrice: double.parse(_controllers["purchasePrice"]!.text),
          salePrice: double.parse(_controllers["salePrice"]!.text),
          quantity: int.parse(_controllers["quantity"]!.text),
          description: _controllers["description"]!.text,
          category: _controllers["category"]!.text,
          imageUrl: _controllers["imageUrl"]!.text,
          isActive: _isActive,
          isOnSale: _isOnSale,
          discount: _discount,
        ),
      );
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Novo Produto")),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: Form(
          key: _formKey,
          child: ListView(
            children: [
              _buildTextField("name", "Nome*"),
              _buildTextField("purchasePrice", "Preço de Compra*", isNumber: true),
              _buildTextField("salePrice", "Preço de Venda*", isNumber: true),
              _buildTextField("quantity", "Quantidade*", isNumber: true),
              _buildTextField("description", "Descrição*", maxLines: 3),
              _buildTextField("category", "Categoria*"),
              _buildTextField("imageUrl", "URL da Imagem*"),
              SwitchListTile(title: const Text("Ativo"), value: _isActive, onChanged: (v) => setState(() => _isActive = v)),
              CheckboxListTile(title: const Text("Promoção"), value: _isOnSale, onChanged: (v) => setState(() => _isOnSale = v!)),
              if (_isOnSale) ...[
                Text("Desconto: ${_discount.round()}%"),
                Slider(value: _discount, max: 100, onChanged: (v) => setState(() => _discount = v)),
              ],
              ElevatedButton(onPressed: _saveProduct, child: const Text("Salvar")),
            ],
          ),
        ),
      ),
    );
  }
}

class ProductDetailScreen extends StatelessWidget {
  final Product product;
  const ProductDetailScreen({required this.product});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(product.name)),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: ListView(
          children: [
            Center(
              child: Image.network(
                product.imageUrl,
                height: 200,
                errorBuilder: (_, __, ___) => const Icon(Icons.error, size: 100),
              ),
            ),
            const SizedBox(height: 16),
            Text("Preço: R\$${product.salePrice.toStringAsFixed(2)}", style: const TextStyle(fontSize: 18)),
            Text("Estoque: ${product.quantity}"),
            Text("Categoria: ${product.category}"),
            const SizedBox(height: 8),
            const Text("Descrição:", style: TextStyle(fontWeight: FontWeight.bold)),
            Text(product.description),
            const SizedBox(height: 8),
            Row(
              children: [
                Icon(Icons.check_circle, color: product.isActive ? Colors.green : Colors.grey),
                Text(" ${product.isActive ? "Ativo" : "Inativo"}"),
              ],
            ),
            if (product.isOnSale)
              Text("🔥 PROMOÇÃO: ${product.discount}% OFF", style: const TextStyle(color: Colors.red)),
          ],
        ),
      ),
    );
  }
}
