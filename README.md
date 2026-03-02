# 🍕 Projeto Completo — Restaurante & Pizzaria Sabor 84
Este repositório entrega uma solução full stack com **landing page moderna** + **API Java Spring Boot** para um restaurante familiar que funciona à noite e fins de semana.



## 1) Estrutura de pastas (bem simples)

```bash
Restaurante-landing-page/
├── frontend/
│   ├── index.html
│   ├── css/styles.css
│   └── js/app.js
├── backend/
│   ├── pom.xml
│   ├── Dockerfile
│   ├── docker-compose.yml
│   └── src/main/java/com/restaurante/
│       ├── controller/
│       ├── service/
│       ├── repository/
│       ├── model/
│       ├── dto/
│       ├── security/
│       └── config/
└── README.md
```

## 2) Front-end (landing page)

### Seções implementadas
- Cabeçalho
- Banner principal
- Sobre
- Cardápio com filtro por categoria
- Depoimentos
- Reserva de mesa
- Botão **Peça Agora** com modal de pedido
- Localização com mapa
- Rodapé

### Tecnologias
- HTML5
- CSS3 (Flex/Grid)
- JS moderno (ES6+)
- `fetch()` para integração com API

### Rodar o front local
1. Entre na pasta `frontend`.
2. Rode um servidor estático:
   ```bash
   python3 -m http.server 5500
   ```
3. Abra `http://localhost:5500`.

## 3) Back-end (Java 17 + Spring Boot)

### O que cada pasta significa (como criança)
- **controller**: “porta de entrada” da API (recebe pedidos HTTP).
- **service**: “cérebro” (regras de negócio).
- **repository**: “mão que mexe no banco”.
- **model**: “caixas de dados” (entidades/tabelas).
- **dto**: “pacotinhos de entrada/saída da API”.
- **security**: login, JWT e proteção de rotas.

### Entidades
- Produto
- Categoria
- Pedido
- ItemPedido
- Reserva
- Usuario

### Endpoints obrigatórios
- `GET /cardapio`
- `GET /cardapio/{categoria}`
- `POST /pedido`
- `GET /pedido/{id}`
- `POST /reserva`
- `GET /reserva/dia/{data}`
- `POST /auth/login`
- `GET /relatorios/faturamento-dia?data=YYYY-MM-DD`

### Regras de negócio implementadas
- Validação de entrada via Bean Validation
- Cálculo automático do total do pedido
- Baixa de estoque ao criar pedido
- Geração de JWT no login
- Rota interna protegida (`/relatorios/**`)

### PostgreSQL local (passo a passo)
1. Instale PostgreSQL.
2. Crie banco `restaurante`.
3. Usuário/senha padrão no projeto: `postgres/postgres`.
4. Ajuste variáveis:
   - `DB_URL`
   - `DB_USER`
   - `DB_PASS`

### Rodar o back local
```bash
cd backend
mvn spring-boot:run
```

## 4) Integração front ↔ back (fetch)

### Exemplo GET
```js
const res = await fetch('http://localhost:8080/cardapio');
const data = await res.json();
```

### Exemplo POST JSON
```js
const payload = { nomeCliente: 'Ana', itens: [{ produtoId: 1, quantidade: 2 }] };
const res = await fetch('http://localhost:8080/pedido', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify(payload)
});
const pedido = await res.json();
```

### Usando JWT no navegador
```js
localStorage.setItem('jwtToken', token);
fetch('/relatorios/faturamento-dia?data=2026-03-01', {
  headers: { Authorization: `Bearer ${localStorage.getItem('jwtToken')}` }
});
```

### Erros comuns
- Backend desligado → erro de conexão
- Categoria inválida → 400/500
- Token expirado → 401/403
- Estoque insuficiente → erro de negócio

## 5) Automação implementada
- Carregamento automático do cardápio ao abrir a página.
- Botões **Peça Agora** abrem modal.
- Cálculo automático do total no front e no back.
- Toasts de feedback visual.
- Validação nos dois lados.

## 6) Deploy completo

### Front-end (GitHub Pages/Netlify/Vercel)
1. Suba repo no GitHub.
2. Aponte publish para pasta `frontend`.
3. Configure variável de ambiente (quando houver) com URL da API.

### Back-end (Render/Railway)
1. Build command: `mvn clean package -DskipTests`
2. Start command: `java -jar target/backend-0.0.1-SNAPSHOT.jar`
3. Variáveis:
   - `DB_URL`
   - `DB_USER`
   - `DB_PASS`
   - `JWT_SECRET`

### Banco na nuvem
1. Crie instância PostgreSQL no Railway/Supabase/Neon.
2. Copie host/porta/db/user/pass.
3. Preencha no serviço do backend.

## 7) Docker
```bash
cd backend
docker compose up --build
```

## 8) Exemplos de requisição (API)

### Login
```bash
curl -X POST http://localhost:8080/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","senha":"123456"}'
```

### Cardápio
```bash
curl http://localhost:8080/cardapio
```

### Reserva
```bash
curl -X POST http://localhost:8080/reserva \
  -H "Content-Type: application/json" \
  -d '{"nomeCliente":"João","telefone":"1199999-9999","dataHora":"2026-03-01T20:30:00","quantidadePessoas":4}'
```

## 9) Como atualizar cardápio no futuro
- Opção rápida: inserir novos produtos no banco PostgreSQL.
- Opção ideal: criar CRUD administrativo para Produto/Categoria.
- Como o front consome `/cardapio`, qualquer item novo já aparece automaticamente.
