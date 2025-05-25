import 'package:flutter/material.dart';

void main() => runApp(const ProdutoApp());

class ProdutoApp extends StatelessWidget {
  const ProdutoApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Cadastro de Produtos',
      theme: ThemeData(primarySwatch: Colors.teal),
      home: const CadastroProdutoScreen(),
    );
  }
}

class Produto {
  String nome;
  double precoCompra;
  double precoVenda;
  int quantidade;
  String descricao;
  String categoria;
  String imagemUrl;
  bool ativo;
  bool promocao;
  double desconto;

  Produto({
    required this.nome,
    required this.precoCompra,
    required this.precoVenda,
    required this.quantidade,
    required this.descricao,
    required this.categoria,
    required this.imagemUrl,
    required this.ativo,
    required this.promocao,
    required this.desconto,
  });
}

List<Produto> produtos = [
  Produto(
    nome: 'Vestido de Inverno',
    precoCompra: 80,
    precoVenda: 149.99,
    quantidade: 5,
    descricao: 'Vestido elegante para o inverno.',
    categoria: 'Roupas',
    imagemUrl: 'https://i.imgur.com/WsYCM0e.png',
    ativo: true,
    promocao: true,
    desconto: 20,
  ),
  Produto(
    nome: 'Bota de Couro',
    precoCompra: 120,
    precoVenda: 199.99,
    quantidade: 3,
    descricao: 'Bota de couro legítimo, ideal para o frio.',
    categoria: 'Calçados',
    imagemUrl: 'https://i.imgur.com/J8YShQf.png',
    ativo: true,
    promocao: false,
    desconto: 0,
  ),
];

class CadastroProdutoScreen extends StatefulWidget {
  const CadastroProdutoScreen({super.key});

  @override
  State<CadastroProdutoScreen> createState() => _CadastroProdutoScreenState();
}

class _CadastroProdutoScreenState extends State<CadastroProdutoScreen> {
  final _formKey = GlobalKey<FormState>();
  final TextEditingController _nome = TextEditingController();
  final TextEditingController _precoCompra = TextEditingController();
  final TextEditingController _precoVenda = TextEditingController();
  final TextEditingController _quantidade = TextEditingController();
  final TextEditingController _descricao = TextEditingController();
  final TextEditingController _categoria = TextEditingController();
  final TextEditingController _imagemUrl = TextEditingController();

  bool _ativo = false;
  bool _promocao = false;
  double _desconto = 0;

  void _cadastrarProduto() {
    if (_formKey.currentState!.validate()) {
      setState(() {
        produtos.add(Produto(
          nome: _nome.text,
          precoCompra: double.parse(_precoCompra.text),
          precoVenda: double.parse(_precoVenda.text),
          quantidade: int.parse(_quantidade.text),
          descricao: _descricao.text,
          categoria: _categoria.text,
          imagemUrl: _imagemUrl.text,
          ativo: _ativo,
          promocao: _promocao,
          desconto: _desconto,
        ));
      });
      Navigator.push(
        context,
        MaterialPageRoute(builder: (context) => const ListaProdutosScreen()),
      );
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Cadastrar Produto')),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: Form(
          key: _formKey,
          child: ListView(
            children: [
              TextFormField(
                controller: _nome,
                decoration: const InputDecoration(labelText: 'Nome'),
                validator: (value) => value!.isEmpty ? 'Campo obrigatório' : null,
              ),
              TextFormField(
                controller: _precoCompra,
                keyboardType: TextInputType.number,
                decoration: const InputDecoration(labelText: 'Preço de Compra'),
                validator: (value) => value!.isEmpty ? 'Campo obrigatório' : null,
              ),
              TextFormField(
                controller: _precoVenda,
                keyboardType: TextInputType.number,
                decoration: const InputDecoration(labelText: 'Preço de Venda'),
                validator: (value) => value!.isEmpty ? 'Campo obrigatório' : null,
              ),
              TextFormField(
                controller: _quantidade,
                keyboardType: TextInputType.number,
                decoration: const InputDecoration(labelText: 'Quantidade'),
                validator: (value) => value!.isEmpty ? 'Campo obrigatório' : null,
              ),
              TextFormField(
                controller: _descricao,
                decoration: const InputDecoration(labelText: 'Descrição'),
                validator: (value) => value!.isEmpty ? 'Campo obrigatório' : null,
              ),
              TextFormField(
                controller: _categoria,
                decoration: const InputDecoration(labelText: 'Categoria'),
              ),
              TextFormField(
                controller: _imagemUrl,
                decoration: const InputDecoration(labelText: 'URL da Imagem'),
              ),
              SwitchListTile(
                title: const Text('Produto Ativo'),
                value: _ativo,
                onChanged: (val) => setState(() => _ativo = val),
              ),
              CheckboxListTile(
                title: const Text('Em Promoção'),
                value: _promocao,
                onChanged: (val) => setState(() => _promocao = val!),
              ),
              Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  const Text('Desconto (%)'),
                  Slider(
                    value: _desconto,
                    min: 0,
                    max: 100,
                    divisions: 20,
                    label: '${_desconto.round()}%',
                    onChanged: (val) => setState(() => _desconto = val),
                  ),
                ],
              ),
              const SizedBox(height: 20),
              ElevatedButton(
                onPressed: _cadastrarProduto,
                child: const Text('Cadastrar Produto'),
              )
            ],
          ),
        ),
      ),
    );
  }
}

class ListaProdutosScreen extends StatelessWidget {
  const ListaProdutosScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Lista de Produtos')),
      body: ListView.builder(
        itemCount: produtos.length,
        itemBuilder: (context, index) {
          final produto = produtos[index];
          return ListTile(
            leading: Image.network(
              produto.imagemUrl,
              width: 50,
              height: 50,
              errorBuilder: (context, error, stackTrace) => const Icon(Icons.error),
            ),
            title: Text(produto.nome),
            subtitle: Text('R\$ ${produto.precoVenda.toStringAsFixed(2)}'),
            onTap: () => Navigator.push(
              context,
              MaterialPageRoute(
                builder: (context) => DetalhesProdutoScreen(produto: produto),
              ),
            ),
          );
        },
      ),
    );
  }
}

class DetalhesProdutoScreen extends StatelessWidget {
  final Produto produto;
  const DetalhesProdutoScreen({super.key, required this.produto});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(produto.nome)),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: ListView(
          children: [
            Image.network(
              produto.imagemUrl,
              height: 200,
              fit: BoxFit.cover,
              errorBuilder: (context, error, stackTrace) => const Icon(Icons.error, size: 100),
            ),
            const SizedBox(height: 16),
            Text('Nome: ${produto.nome}', style: const TextStyle(fontSize: 18)),
            Text('Categoria: ${produto.categoria}'),
            Text('Descrição: ${produto.descricao}'),
            Text('Preço de Compra: R\$ ${produto.precoCompra.toStringAsFixed(2)}'),
            Text('Preço de Venda: R\$ ${produto.precoVenda.toStringAsFixed(2)}'),
            Text('Quantidade: ${produto.quantidade}'),
            Row(
              children: [
                const Text('Ativo: '),
                Icon(produto.ativo ? Icons.check_circle : Icons.cancel, color: produto.ativo ? Colors.green : Colors.red),
              ],
            ),
            Row(
              children: [
                const Text('Promoção: '),
                Icon(produto.promocao ? Icons.local_offer : Icons.close, color: produto.promocao ? Colors.orange : Colors.grey),
              ],
            ),
            Text('Desconto: ${produto.desconto.toStringAsFixed(0)}%'),
          ],
        ),
      ),
    );
  }
}
