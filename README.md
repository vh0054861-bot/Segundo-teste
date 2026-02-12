# Segundo-testeconst express = require("express");
const Database = require("better-sqlite3");

const app = express();
const db = new Database("database.db");

app.use(express.urlencoded({ extended: true }));
app.use(express.json());

/* ================== BANCO ================== */
db.exec(`
CREATE TABLE IF NOT EXISTS clientes (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nome TEXT
);

CREATE TABLE IF NOT EXISTS produtos (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nome TEXT,
    preco REAL,
    estoque INTEGER
);

CREATE TABLE IF NOT EXISTS vendas (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    cliente TEXT,
    total REAL,
    data TEXT
);
`);

/* ================== INTERFACE ================== */

app.get("/", (req, res) => {
res.send(`
<!DOCTYPE html>
<html>
<head>
<title>Sistema de Vendas</title>
<style>
body { font-family: Arial; margin:40px; background:#f4f4f4;}
h1{color:#333;}
section{background:white;padding:20px;margin-bottom:20px;border-radius:8px;}
input,button{padding:8px;margin:5px;}
button{background:#2c7be5;color:white;border:none;border-radius:5px;}
table{width:100%;margin-top:10px;border-collapse:collapse;}
td,th{border:1px solid #ddd;padding:8px;}
</style>
</head>
<body>

<h1>Sistema de Controle de Vendas</h1>

<section>
<h2>Cadastrar Cliente</h2>
<form method="POST" action="/cliente">
<input name="nome" placeholder="Nome do cliente" required/>
<button>Cadastrar</button>
</form>
</section>

<section>
<h2>Cadastrar Produto</h2>
<form method="POST" action="/produto">
<input name="nome" placeholder="Nome do produto" required/>
<input name="preco" type="number" step="0.01" placeholder="Preço" required/>
<input name="estoque" type="number" placeholder="Estoque" required/>
<button>Cadastrar</button>
</form>
</section>

<section>
<h2>Registrar Venda</h2>
<form method="POST" action="/venda">
<input name="cliente" placeholder="Nome do cliente" required/>
<input name="total" type="number" step="0.01" placeholder="Total da venda" required/>
<button>Registrar</button>
</form>
</section>

<section>
<h2>Produtos</h2>
${listarProdutos()}
</section>

<section>
<h2>Vendas</h2>
${listarVendas()}
</section>

</body>
</html>
`);
});

/* ================== ROTAS ================== */

app.post("/cliente", (req,res)=>{
    db.prepare("INSERT INTO clientes (nome) VALUES (?)")
    .run(req.body.nome);
    res.redirect("/");
});

app.post("/produto", (req,res)=>{
    db.prepare("INSERT INTO produtos (nome,preco,estoque) VALUES (?,?,?)")
    .run(req.body.nome, req.body.preco, req.body.estoque);
    res.redirect("/");
});

app.post("/venda", (req,res)=>{
    db.prepare("INSERT INTO vendas (cliente,total,data) VALUES (?,?,datetime('now'))")
    .run(req.body.cliente, req.body.total);
    res.redirect("/");
});

/* ================== FUNÇÕES ================== */

function listarProdutos(){
    const produtos = db.prepare("SELECT * FROM produtos").all();
    let html = "<table><tr><th>ID</th><th>Nome</th><th>Preço</th><th>Estoque</th></tr>";
    produtos.forEach(p=>{
        html += `<tr>
        <td>${p.id}</td>
        <td>${p.nome}</td>
        <td>R$ ${p.preco}</td>
        <td>${p.estoque}</td>
        </tr>`;
    });
    html += "</table>";
    return html;
}

function listarVendas(){
    const vendas = db.prepare("SELECT * FROM vendas").all();
    let html = "<table><tr><th>ID</th><th>Cliente</th><th>Total</th><th>Data</th></tr>";
    vendas.forEach(v=>{
        html += `<tr>
        <td>${v.id}</td>
        <td>${v.cliente}</td>
        <td>R$ ${v.total}</td>
        <td>${v.data}</td>
        </tr>`;
    });
    html += "</table>";
    return html;
}

/* ================== SERVIDOR ================== */

app.listen(3000, ()=>{
    console.log("Rodando em http://localhost:3000");
});
