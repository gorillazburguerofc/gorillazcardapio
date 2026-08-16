<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cardápio Digital - Gorillaz Burguer</title>
    <style>
        :root {
            --primary: #f59e0b; /* Laranja/Amarelo Gorillas */
            --bg-dark: #121212;
            --bg-card: #1e1e1e;
            --text-light: #f8fafc;
            --success: #10b981;
        }
        body { 
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; 
            background-color: var(--bg-dark); 
            color: var(--text-light); 
            margin: 0; 
            padding: 0; 
            padding-bottom: 80px;
        }
        header { 
            background: #000; 
            padding: 20px; 
            text-align: center; 
            border-bottom: 3px solid var(--primary); 
            position: sticky;
            top: 0;
            z-index: 100;
        }
        header h1 { margin: 0; color: var(--primary); font-size: 24px;}
        header p { margin: 5px 0 0 0; color: #aaa; font-size: 14px;}
        
        .container { max-width: 600px; margin: 0 auto; padding: 15px; }
        
        .category { 
            font-size: 1.2em; 
            font-weight: bold; 
            margin-top: 25px; 
            border-bottom: 1px solid #333; 
            padding-bottom: 8px; 
            color: var(--primary); 
            text-transform: uppercase;
            letter-spacing: 1px;
        }
        
        .item { 
            display: flex; 
            justify-content: space-between; 
            align-items: center; 
            background: var(--bg-card); 
            padding: 15px; 
            margin-top: 12px; 
            border-radius: 8px; 
            border: 1px solid #2a2a2a;
        }
        .item-info { flex: 1; padding-right: 15px; }
        .item-info h3 { margin: 0 0 5px 0; font-size: 16px; color: #fff;}
        .item-info p { margin: 0; font-size: 13px; color: #94a3b8; line-height: 1.4; }
        .price { font-weight: bold; color: var(--success); margin-top: 8px; font-size: 15px;}
        
        .btn-add { 
            background: var(--primary); 
            color: #000; 
            border: none; 
            padding: 10px 15px; 
            border-radius: 6px; 
            font-weight: bold; 
            cursor: pointer; 
            transition: 0.2s;
        }
        .btn-add:active { transform: scale(0.95); }
        
        /* Carrinho Flutuante */
        .cart-bar { 
            position: fixed; 
            bottom: 0; 
            width: 100%; 
            background: var(--primary); 
            color: #000; 
            padding: 18px; 
            text-align: center; 
            font-weight: bold; 
            cursor: pointer; 
            box-sizing: border-box; 
            font-size: 1.1em; 
            display: none; 
            box-shadow: 0 -4px 10px rgba(0,0,0,0.3);
            z-index: 999;
        }
        
        /* Modal do Carrinho */
        .modal { 
            display: none; 
            position: fixed; 
            top: 0; 
            left: 0; 
            width: 100%; 
            height: 100%; 
            background: rgba(0,0,0,0.85); 
            z-index: 1000; 
            overflow-y: auto;
        }
        .modal-content { 
            background: var(--bg-card); 
            max-width: 500px; 
            margin: 0 auto; 
            min-height: 100vh;
            padding: 20px; 
            box-sizing: border-box;
        }
        .close { 
            float: right; 
            font-size: 28px; 
            cursor: pointer; 
            color: var(--primary); 
            line-height: 1;
        }
        
        .cart-item { 
            display: flex; 
            justify-content: space-between; 
            align-items: center;
            margin-bottom: 15px; 
            border-bottom: 1px dashed #333; 
            padding-bottom: 15px; 
        }
        .cart-controls button {
            padding: 8px 12px;
            background: #333;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
            font-weight: bold;
        }
        
        .form-group { margin-bottom: 15px; }
        label { display: block; margin-bottom: 5px; font-size: 14px; color: #cbd5e1; }
        input, select { 
            width: 100%; 
            padding: 12px; 
            border-radius: 6px; 
            border: 1px solid #475569; 
            background: #0f172a; 
            color: white; 
            box-sizing: border-box;
            font-size: 16px;
        }
        
        .btn-send { 
            background: var(--success); 
            color: white; 
            border: none; 
            width: 100%; 
            padding: 18px; 
            font-size: 1.1em; 
            border-radius: 8px; 
            font-weight: bold; 
            cursor: pointer; 
            margin-top: 10px;
            margin-bottom: 40px;
        }
    </style>
</head>
<body>
    <header>
        <h1>🦍 GORILLAZ BURGUER</h1>
        <p>O melhor delivery de Parnaíba!</p>
    </header>
    
    <div class="container">
        <div id="menu-items"></div>
    </div>

    <!-- Barra inferior do carrinho -->
    <div class="cart-bar" id="cart-bar" onclick="openCart()">
        🛒 Ver Pedido (<span id="cart-count">0</span>) - R$ <span id="cart-total-bar">0.00</span>
    </div>

    <!-- Tela do Carrinho (Modal) -->
    <div class="modal" id="cart-modal">
        <div class="modal-content">
            <span class="close" onclick="closeCart()">&times;</span>
            <h2 style="color: var(--primary); margin-top: 0;">Seu Pedido</h2>
            
            <div id="cart-items-list"></div>
            
            <h3 style="display: flex; justify-content: space-between; border-bottom: 2px solid #333; padding-bottom: 10px;">
                <span>Total:</span> 
                <span style="color: var(--success);">R$ <span id="cart-total-modal">0.00</span></span>
            </h3>
            
            <h3 style="color: var(--primary); margin-top: 25px;">Para Onde Vamos Mandar?</h3>
            
            <div class="form-group">
                <label>Seu Nome</label>
                <input type="text" id="c-name" placeholder="Como devemos te chamar?">
            </div>
            
            <div class="form-group">
                <label>Endereço Completo (Rua, Número, Bairro)</label>
                <input type="text" id="c-address" placeholder="Ex: Rua A, 123 - Centro">
            </div>
            
            <div class="form-group">
                <label>Observação (Opcional)</label>
                <input type="text" id="c-obs" placeholder="Ex: Tirar cebola, ponto da carne...">
            </div>
            
            <h3 style="color: var(--primary); margin-top: 25px;">Pagamento</h3>
            <div class="form-group">
                <label>Forma de Pagamento na Entrega</label>
                <select id="c-payment">
                    <option value="PIX">PIX</option>
                    <option value="Cartão de Crédito">Cartão de Crédito</option>
                    <option value="Cartão de Débito">Cartão de Débito</option>
                    <option value="Dinheiro">Dinheiro</option>
                </select>
            </div>
            
            <div class="form-group" id="troco-div" style="display:none;">
                <label>Precisa de troco para quanto?</label>
                <input type="number" id="c-troco" placeholder="Ex: 50">
            </div>

            <button class="btn-send" onclick="sendOrder()">Enviar Pedido no WhatsApp 📱</button>
        </div>
    </div>

    <script>
        // SEU NÚMERO DE WHATSAPP DA GORILLAZ JÁ CONFIGURADO AQUI
        const phone = "5586994466786"; 
        
        // SEU CARDÁPIO - VOCÊ PODE EDITAR OS PRODUTOS AQUI FACILMENTE
        const menu = [
            { id: 1, category: "🍔 Combos VIP", name: "Combo Oculto", desc: "Pão brioche, blend 180g, duplo cheddar, bacon + Fritas + Refri. O especial do cardápio!", price: 40.00 },
            { id: 2, category: "🍔 Combos VIP", name: "Combo Clássico", desc: "Pão brioche, blend 180g, queijo prato, salada fresca + Fritas", price: 32.00 },
            { id: 3, category: "🍔 Hambúrgueres", name: "Gorilla Smash", desc: "Pão brioche, 2x smash 90g, duplo cheddar derretido", price: 25.00 },
            { id: 4, category: "🍟 Porções", name: "Batata Frita Tradicional", desc: "Porção individual super crocante", price: 12.00 },
            { id: 5, category: "🥤 Bebidas", name: "Coca-Cola Lata", desc: "350ml trincando de gelada", price: 6.00 },
            { id: 6, category: "🥤 Bebidas", name: "Guaraná Lata", desc: "350ml trincando de gelada", price: 6.00 }
        ];

        let cart = [];

        function renderMenu() {
            let html = '';
            let currentCategory = '';
            
            menu.forEach(item => {
                if (item.category !== currentCategory) {
                    html += `<div class="category">${item.category}</div>`;
                    currentCategory = item.category;
                }
                html += `
                    <div class="item">
                        <div class="item-info">
                            <h3>${item.name}</h3>
                            <p>${item.desc}</p>
                            <div class="price">R$ ${item.price.toFixed(2)}</div>
                        </div>
                        <button class="btn-add" onclick="addToCart(${item.id})">+ Add</button>
                    </div>
                `;
            });
            document.getElementById('menu-items').innerHTML = html;
        }

        function addToCart(id) {
            const item = menu.find(i => i.id === id);
            const cartItem = cart.find(i => i.id === id);
            
            if (cartItem) {
                cartItem.qtd++;
            } else {
                cart.push({ ...item, qtd: 1 });
            }
            updateCart();
            
            // Efeito visual no botão
            const btn = event.target;
            const originalText = btn.innerText;
            btn.innerText = "✓ Adicionado";
            btn.style.background = "#10b981";
            btn.style.color = "white";
            setTimeout(() => {
                btn.innerText = originalText;
                btn.style.background = "var(--primary)";
                btn.style.color = "#000";
            }, 1000);
        }

        function updateCart() {
            let count = 0;
            let total = 0;
            
            cart.forEach(item => {
                count += item.qtd;
                total += item.price * item.qtd;
            });
            
            document.getElementById('cart-count').innerText = count;
            document.getElementById('cart-total-bar').innerText = total.toFixed(2);
            document.getElementById('cart-total-modal').innerText = total.toFixed(2);
            
            if (count > 0) {
                document.getElementById('cart-bar').style.display = 'block';
            } else {
                document.getElementById('cart-bar').style.display = 'none';
                closeCart();
            }
            renderCartItems();
        }

        function renderCartItems() {
            let html = '';
            cart.forEach((item, index) => {
                html += `
                    <div class="cart-item">
                        <div>
                            <div style="font-weight: bold; color: #fff;">${item.name}</div>
                            <div style="color: var(--success); font-weight: bold; margin-top: 5px;">
                                R$ ${(item.price * item.qtd).toFixed(2)} 
                                <span style="color: #64748b; font-size: 12px; font-weight: normal;">(R$ ${item.price.toFixed(2)} un)</span>
                            </div>
                        </div>
                        <div class="cart-controls">
                            <button onclick="changeQtd(${index}, -1)">-</button>
                            <span style="margin: 0 10px; font-weight: bold;">${item.qtd}</span>
                            <button onclick="changeQtd(${index}, 1)">+</button>
                        </div>
                    </div>
                `;
            });
            document.getElementById('cart-items-list').innerHTML = html || '<p style="color: #64748b;">Seu carrinho está vazio.</p>';
        }

        function changeQtd(index, delta) {
            cart[index].qtd += delta;
            if (cart[index].qtd <= 0) {
                cart.splice(index, 1);
            }
            updateCart();
        }

        function openCart() { 
            document.getElementById('cart-modal').style.display = 'block'; 
            document.body.style.overflow = 'hidden'; 
        }
        
        function closeCart() { 
            document.getElementById('cart-modal').style.display = 'none'; 
            document.body.style.overflow = 'auto';
        }

        document.getElementById('c-payment').addEventListener('change', function() {
            if(this.value === 'Dinheiro') {
                document.getElementById('troco-div').style.display = 'block';
            } else {
                document.getElementById('troco-div').style.display = 'none';
            }
        });

        function sendOrder() {
            const name = document.getElementById('c-name').value.trim();
            const address = document.getElementById('c-address').value.trim();
            const obs = document.getElementById('c-obs').value.trim();
            const payment = document.getElementById('c-payment').value;
            const troco = document.getElementById('c-troco').value;

            if (!name || !address) {
                alert("🦍 Chefe, preencha seu nome e endereço para a gente entregar!");
                return;
            }

            // Montar texto pro WhatsApp formatado bonitinho
            let text = "🦍 *NOVO PEDIDO - GORILLAZ BURGUER* 🦍%0A%0A";
            
            text += "*🛒 ITENS DO PEDIDO:*%0A";
            let total = 0;
            cart.forEach(item => {
                text += `▪️ ${item.qtd}x ${item.name} - R$ ${(item.price * item.qtd).toFixed(2)}%0A`;
                total += item.price * item.qtd;
            });
            
            text += `%0A*💰 TOTAL:* R$ ${total.toFixed(2)}%0A`;
            text += `*💳 PAGAMENTO:* ${payment}`;
            if (payment === 'Dinheiro' && troco) {
                text += ` (Troco para R$ ${troco})`;
            }
            
            text += "%0A%0A*📍 DADOS DA ENTREGA:*%0A";
            text += `*Nome:* ${name}%0A`;
            text += `*Endereço:* ${address}%0A`;
            if (obs) {
                text += `*Observação:* ${obs}%0A`;
            }

            // Redirecionar pro WhatsApp
            const url = `https://wa.me/${phone}?text=${text}`;
            window.open(url, '_blank');
        }

        // Iniciar
        renderMenu();
    </script>
</body>
</html>
