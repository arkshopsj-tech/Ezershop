<!DOCTYPE html>
<html lang="pt">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LojaFácil AI - SaaS Architecture</title>
<script src="https://cdn.tailwindcss.com"></script>
<script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js"></script>

<style>
html { scroll-behavior: smooth; }
body { background: #0f172a; }
.glass {
    background: rgba(255,255,255,0.05);
    backdrop-filter: blur(20px);
    border: 1px solid rgba(255,255,255,0.1);
}
.code-block {
    background: #020617;
    border-radius: 12px;
    padding: 20px;
    overflow-x: auto;
    font-size: 14px;
}
.gradient-text {
    background: linear-gradient(to right,#6366f1,#ec4899);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
}
</style>
</head>

<body class="text-white font-sans">

<!-- HERO -->
<section class="h-screen flex flex-col justify-center items-center text-center px-6">
<h1 class="text-6xl font-bold gradient-text">LojaFácil AI</h1>
<p class="mt-6 text-xl text-gray-400 max-w-2xl">
Plataforma SaaS de E-commerce Multi-Tenant com IA Integrada
</p>
</section>

<!-- ARQUITETURA -->
<section class="py-24 px-10">
<h2 class="text-4xl font-bold text-center mb-16">Arquitetura do Sistema</h2>

<div class="glass p-10 rounded-3xl">

<div class="mermaid">
flowchart TD
User[User] --> Frontend
Frontend --> API
API --> Auth
API --> StoreService
API --> ProductService
API --> OrderService
StoreService --> DB[(PostgreSQL)]
ProductService --> DB
OrderService --> DB
API --> Redis[(Redis Cache)]
OrderService --> Stripe
API --> OpenAI
</div>

</div>
</section>

<!-- POSTGRESQL -->
<section class="py-24 px-10 bg-slate-900">
<h2 class="text-4xl font-bold text-center mb-16">PostgreSQL Schema</h2>

<div class="code-block">
<pre>
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

CREATE TYPE product_status AS ENUM ('active','inactive','archived');
CREATE TYPE payment_status AS ENUM ('pending','paid','failed','refunded');
CREATE TYPE fulfillment_status AS ENUM ('unfulfilled','shipped','delivered','cancelled');

CREATE TABLE plans (
 id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
 name VARCHAR(50) NOT NULL,
 monthly_price DECIMAL(10,2) NOT NULL,
 max_products INTEGER NOT NULL,
 features_json JSONB NOT NULL
);

CREATE TABLE stores (
 id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
 name VARCHAR(255) NOT NULL,
 subdomain VARCHAR(255) UNIQUE NOT NULL,
 description TEXT,
 logo_url TEXT,
 plan_id UUID REFERENCES plans(id),
 template_id UUID,
 owner_id UUID,
 created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE products (
 id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
 store_id UUID REFERENCES stores(id) ON DELETE CASCADE,
 name VARCHAR(255) NOT NULL,
 description TEXT,
 price DECIMAL(10,2) NOT NULL,
 stock_quantity INTEGER DEFAULT 0,
 image_url TEXT,
 status product_status DEFAULT 'active'
);
</pre>
</div>
</section>

<!-- MONGODB -->
<section class="py-24 px-10">
<h2 class="text-4xl font-bold text-center mb-16">MongoDB Models</h2>

<div class="code-block">
<pre>
// Store
{
 name: String,
 subdomain: { type: String, unique: true },
 description: String,
 logo_url: String,
 plan_id: ObjectId,
 created_at: Date
}

// Product
{
 store_id: ObjectId,
 name: String,
 description: String,
 price: Number,
 stock_quantity: Number,
 status: "active" | "inactive" | "archived"
}
</pre>
</div>
</section>

<!-- API REST -->
<section class="py-24 px-10 bg-slate-900">
<h2 class="text-4xl font-bold text-center mb-16">API REST Endpoints</h2>

<div class="code-block">
<pre>
POST   /api/auth/register
POST   /api/auth/login

POST   /api/stores
GET    /api/stores/:id
PUT    /api/stores/:id
DELETE /api/stores/:id

POST   /api/products
GET    /api/products?store_id=
PUT    /api/products/:id
DELETE /api/products/:id

POST   /api/orders
GET    /api/orders?store_id=
</pre>
</div>
</section>

<!-- INVESTOR SLIDES -->
<section class="py-24 px-10">
<h2 class="text-4xl font-bold text-center mb-16">Investor Pitch</h2>

<div class="grid md:grid-cols-2 gap-10">

<div class="glass p-8 rounded-3xl">
<h3 class="text-2xl font-bold mb-4">Problema</h3>
<p class="text-gray-400">
Pequenos negócios têm dificuldade em criar lojas online rápidas, profissionais e escaláveis.
</p>
</div>

<div class="glass p-8 rounded-3xl">
<h3 class="text-2xl font-bold mb-4">Solução</h3>
<p class="text-gray-400">
Criação de loja completa em menos de 3 minutos com IA integrada.
</p>
</div>

<div class="glass p-8 rounded-3xl">
<h3 class="text-2xl font-bold mb-4">Modelo de Receita</h3>
<p class="text-gray-400">
Assinatura mensal + taxa por transação + add-ons premium.
</p>
</div>

<div class="glass p-8 rounded-3xl">
<h3 class="text-2xl font-bold mb-4">Visão</h3>
<p class="text-gray-400">
Ser o Shopify da África com IA nativa.
</p>
</div>

</div>
</section>

<footer class="py-10 text-center text-gray-500">
© 2026 LojaFácil AI – Built for Global Scale
</footer>

<script>
mermaid.initialize({ startOnLoad: true, theme: "dark" });
</script>

</body>
</html>
