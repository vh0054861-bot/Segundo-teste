<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Sistema de Vendas</title>
<style>
body{font-family:Arial;margin:40px;background:#f4f4f4}
h1{color:#222}
section{background:#fff;padding:20px;margin-bottom:20px;border-radius:8px;box-shadow:0 2px 6px rgba(0,0,0,0.1)}
input,button{padding:8px;margin:5px}
button{background:#2c7be5;color:#fff;border:none;border-radius:5px;cursor:pointer}
button:hover{background:#1a5ed6}
table{width:100%;border-collapse:collapse;margin-top:10px}
th,td{border:1px solid #ddd;padding:8px;text-align:left}
th{background:#2c7be5;color:white}
</style>
</head>
<body>

<h1>Sistema de Controle de Vendas</h1>

<section>
<h2>Cadastrar Produto</h2>
<input id="nomeProduto" placeholder="Nome do Produto">
<input id="precoProduto" type="number" placeholder="Preço">
<input id="estoqueProduto" type="number" placeholder="Estoque">
<button onclick="addProduto()">Adicionar</button>
</section>

<section>
<h2>Registrar Venda</h2>
<input id="clienteVenda" placeholder="Nome do Cliente">
<input id="valorVenda" type="number" placeholder="Valor da Venda">
<button onclick="addVenda()">Registrar</button>
</section>

<section>
<h2>Produtos</h2>
<div id="listaProdutos"></div>
</section>

<section>
<h2>Vendas</h2>
<div id="listaVendas"></div>
</section>

<script>
let produtos = JSON.parse(localStorage.getItem("produtos")) || [];
let vendas = JSON.parse(localStorage.getItem("vendas")) || [];

function salvar(){
localStorage.setItem("produtos",JSON.stringify(produtos));
localStorage.setItem("vendas",JSON.stringify(vendas));
}

function addProduto(){
let nome=document.getElementById("nomeProduto").value;
let preco=document.getElementById("precoProduto").value;
let estoque=document.getElementById("estoqueProduto").value;

if(!nome || !preco || !estoque) return alert("Preencha tudo");

produtos.push({id:Date.now(),nome,preco,estoque});
salvar();
render();
}

function addVenda(){
let cliente=document.getElementById("clienteVenda").value;
let valor=document.getElementById("valorVenda").value;

if(!cliente || !valor) return alert("Preencha tudo");

vendas.push({id:Date.now(),cliente,valor,data:new Date().toLocaleString()});
salvar();
render();
}

function render(){
let htmlP="<table><tr><th>Nome</th><th>Preço</th><th>Estoque</th></tr>";
produtos.forEach(p=>{
htmlP+=`<tr><td>${p.nome}</td><td>R$ ${p.preco}</td><td>${p.estoque}</td></tr>`;
});
htmlP+="</table>";
document.getElementById("listaProdutos").innerHTML=htmlP;

let htmlV="<table><tr><th>Cliente</th><th>Valor</th><th>Data</th></tr>";
vendas.forEach(v=>{
htmlV+=`<tr><td>${v.cliente}</td><td>R$ ${v.valor}</td><td>${v.data}</td></tr>`;
});
htmlV+="</table>";
document.getElementById("listaVendas").innerHTML=htmlV;
}

render();
</script>

</body>
</html>
