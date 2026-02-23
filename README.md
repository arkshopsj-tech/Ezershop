# 🚀 LojaFácil AI

> Plataforma SaaS Multi-Tenant de E-commerce com IA Integrada  
> Arquitetura escalável pronta para milhões de usuários.

![Status](https://img.shields.io/badge/status-production_ready-success)
![Architecture](https://img.shields.io/badge/architecture-multi--tenant-blue)
![Database](https://img.shields.io/badge/database-postgresql%20%7C%20mongodb-orange)
![Cache](https://img.shields.io/badge/cache-redis-red)
![Infra](https://img.shields.io/badge/infra-docker%20%7C%20k8s-blueviolet)
![License](https://img.shields.io/badge/license-MIT-black)

---

# 🌍 Visão Geral

**LojaFácil AI** é uma plataforma SaaS moderna que permite a criação de lojas online completas em menos de 3 minutos com suporte de Inteligência Artificial.

Projetado para:

- Pequenos negócios
- Empreendedores digitais
- Startups
- Marketplace regionais
- Escala continental (África e Global)

---

# 🧠 Principais Recursos

- 🧠 Geração automática de descrições com IA
- 🌐 Subdomínio automático (`minhaloja.lojafacil.ai`)
- 🎨 Templates dinâmicos configuráveis via JSON
- 💳 Sistema de planos escaláveis
- 📦 Gestão completa de produtos
- 🛒 Sistema robusto de pedidos
- 💰 Controle de pagamentos
- 🎟 Sistema de tickets de suporte
- 📊 Estrutura pronta para BI
- 🔐 Autenticação segura
- 🚀 Arquitetura cloud-ready

---

# 🏗 Arquitetura Geral do Sistema

```mermaid
flowchart TD

User[User] --> Frontend
Frontend --> API
API --> AuthService
API --> StoreService
API --> ProductService
API --> OrderService
API --> SupportService
API --> AIService

StoreService --> DB[(PostgreSQL)]
ProductService --> DB
OrderService --> DB
SupportService --> DB
AuthService --> DB

API --> Cache[(Redis)]

OrderService --> Stripe
AIService --> OpenAI
API --> EmailService


🧩 Arquitetura Técnica
🔹 Frontend

Next.js

TailwindCSS

ShadCN UI

SSR + ISR

Multi-tenant routing por subdomínio

🔹 Backend

Node.js / NestJS

Arquitetura modular

REST API

Pronto para microservices

🔹 Banco de Dados

PostgreSQL (principal)

MongoDB (opcional analytics/logs)

Redis (cache)

🔹 Infraestrutura

Docker

Kubernetes

CI/CD

Cloudflare

CDN

🗄 Banco de Dados – PostgreSQL Completo
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

CREATE TYPE product_status AS ENUM ('active','inactive','archived');
CREATE TYPE payment_status AS ENUM ('pending','paid','failed','refunded');
CREATE TYPE fulfillment_status AS ENUM ('unfulfilled','shipped','delivered','cancelled');
CREATE TYPE ticket_status AS ENUM ('open','in_progress','resolved','closed');
CREATE TYPE ticket_priority AS ENUM ('low','medium','high','urgent');
CREATE TYPE ticket_type AS ENUM ('WhatsApp','Email','Chat');

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

CREATE TABLE categories (
 id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
 store_id UUID REFERENCES stores(id) ON DELETE CASCADE,
 name VARCHAR(255),
 description TEXT
);

CREATE TABLE products (
 id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
 store_id UUID REFERENCES stores(id) ON DELETE CASCADE,
 name VARCHAR(255) NOT NULL,
 description TEXT,
 price DECIMAL(10,2) NOT NULL,
 stock_quantity INTEGER DEFAULT 0,
 image_url TEXT,
 category_id UUID REFERENCES categories(id),
 status product_status DEFAULT 'active'
);

CREATE TABLE orders (
 id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
 store_id UUID REFERENCES stores(id),
 customer_details JSONB NOT NULL,
 total_amount DECIMAL(10,2),
 payment_status payment_status DEFAULT 'pending',
 fulfillment_status fulfillment_status DEFAULT 'unfulfilled',
 created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE order_items (
 id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
 order_id UUID REFERENCES orders(id) ON DELETE CASCADE,
 product_id UUID REFERENCES products(id),
 quantity INTEGER NOT NULL,
 price_at_purchase DECIMAL(10,2)
);

CREATE TABLE support_tickets (
 id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
 store_id UUID REFERENCES stores(id),
 type ticket_type,
 subject VARCHAR(255),
 status ticket_status DEFAULT 'open',
 priority ticket_priority DEFAULT 'medium',
 created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
🍃 Modelagem MongoDB
// Store
{
  name: String,
  subdomain: String,
  description: String,
  logo_url: String,
  plan_id: ObjectId,
  template_id: ObjectId,
  owner_id: ObjectId,
  created_at: Date
}

// Product
{
  store_id: ObjectId,
  name: String,
  description: String,
  price: Number,
  stock_quantity: Number,
  category_id: ObjectId,
  status: String
}

// Order
{
  store_id: ObjectId,
  customer_details: Object,
  total_amount: Number,
  payment_status: String,
  fulfillment_status: String,
  created_at: Date
}
🌐 API REST Completa
🔐 Autenticação
POST   /api/auth/register
POST   /api/auth/login
POST   /api/auth/logout
GET    /api/auth/me
🏪 Stores
POST   /api/stores
GET    /api/stores/:id
PUT    /api/stores/:id
DELETE /api/stores/:id
🛍 Products
POST   /api/products
GET    /api/products?store_id=
GET    /api/products/:id
PUT    /api/products/:id
DELETE /api/products/:id
🧾 Orders
POST   /api/orders
GET    /api/orders?store_id=
GET    /api/orders/:id
🎟 Support
POST   /api/support
GET    /api/support?store_id=
PUT    /api/support/:id
🔐 Segurança

JWT Authentication

Hash de senha (bcrypt)

Rate limiting

Proteção CORS

Validação com DTO

Sanitização contra SQL Injection

Controle de acesso por store_id

⚡ Escalabilidade

Multi-tenant isolado

Cache com Redis

Pronto para horizontal scaling

Suporte a CDN

Separação de serviços

Possível migração para microservices

📂 Estrutura de Pastas
lojafacil-ai/
│
├── apps/
│   ├── web/
│   ├── admin/
│   └── api/
│
├── packages/
│   ├── ui/
│   ├── database/
│   ├── config/
│   └── utils/
│
├── infrastructure/
│   ├── docker/
│   ├── k8s/
│   └── terraform/
│
├── docs/
│   ├── api/
│   ├── architecture/
│   ├── database/
│   └── pitch/
│
├── docker-compose.yml
├── .env.example
└── README.md
💰 Modelo de Negócio
🎯 Problema

Pequenos negócios não conseguem lançar lojas profissionais rapidamente.

💡 Solução

Criação automática de loja com IA em menos de 3 minutos.

📈 Mercado

Mercado global de e-commerce trilionário com crescimento anual superior a 14%.

💎 Diferencial Competitivo

IA nativa

Setup ultra rápido

Arquitetura SaaS escalável

Pronto para expansão continental

🛣 Roadmap

 Dashboard com BI avançado

 Sistema de afiliados

 Marketplace interno

 Dropshipping automatizado

 App mobile

 Integração WhatsApp oficial

 Pagamentos regionais africanos

📜 Licença

Ezershop

👨‍💻 LojaFácil AI

Construindo o futuro do e-commerce com Inteligência Artificial.
Arquitetura preparada para escala global.
