<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Controle de Estoque</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-bottom: 20px;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: left;
        }
        th {
            background-color: #f2f2f2;
        }
        #overlay {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
        }
        .modal {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: white;
            padding: 20px;
            border-radius: 5px;
            min-width: 300px;
        }
    </style>
</head>
<body>

<h1>Centro de Distribuição</h1>
<div id="loginScreen" class="modal">
    <h2>Login</h2>
    <input type="text" id="username" placeholder="Usuário" required>
    <input type="password" id="password" placeholder="Senha" required>
    <button id="loginBtn">Entrar</button>
    <p id="loginError" style="color: red; display: none;">Usuário ou senha inválidos.</p>
</div>

<div id="mainContent" style="display:none;">
    <h2>Informações do Estabelecimento</h2>
    <form id="establishmentForm">
        <input type="text" id="establishmentName" placeholder="Nome do Estabelecimento" required>
        <input type="text" id="establishmentCNPJ" placeholder="CNPJ" value="12.345.678/0001-90" required>
        <input type="text" id="establishmentAddress" placeholder="Endereço" value="Rua Exemplo, 123 - Cidade - Estado" required>
        <input type="email" id="establishmentEmail" placeholder="Email" value="contato@exemplo.com" required>
        <input type="text" id="establishmentPhone" placeholder="Telefone" value="(11) 1234-5678" required>
        <button type="submit">Salvar Informações</button>
    </form>

    <h2>Cadastro de Usuários</h2>
    <form id="userForm">
        <input type="text" id="userName" placeholder="Nome do Usuário" required>
        <input type="password" id="userPassword" placeholder="Senha do Usuário" required>
        <button type="submit">Cadastrar Usuário</button>
    </form>

    <h2>Lista de Usuários</h2>
    <table id="userTable">
        <thead>
            <tr>
                <th>Nome</th>
                <th>Ações</th>
            </tr>
        </thead>
        <tbody>
            <!-- Usuários cadastrados aparecerão aqui -->
        </tbody>
    </table>

    <h2>Editar Usuário</h2>
    <form id="editUserForm" style="display:none;">
        <select id="editUserSelect" required>
            <option value="">Selecione um Usuário</option>
            <!-- Usuários aparecerão aqui -->
        </select>
        <input type="text" id="editUserName" placeholder="Nome do Usuário" required>
        <input type="password" id="editUserPassword" placeholder="Nova Senha" required>
        <button type="submit">Salvar Alterações</button>
    </form>

    <h2>Cadastro de Produtos</h2>
    <form id="productForm">
        <input type="text" id="productName" placeholder="Nome do Produto" required>
        <input type="number" id="productQuantity" placeholder="Quantidade" required>
        <input type="text" id="productBrand" placeholder="Marca" required>
        <input type="text" id="productLocation" placeholder="Localização" required>
        <input type="number" step="0.01" id="productPrice" placeholder="Preço" required>
        <input type="text" id="productBarcode" placeholder="Código de Barras" required>
        <button type="submit">Cadastrar Produto</button>
    </form>

    <h2>Lista de Produtos</h2>
    <table id="productTable">
        <thead>
            <tr>
                <th>Nome</th>
                <th>Quantidade</th>
                <th>Marca</th>
                <th>Localização</th>
                <th>Preço</th>
                <th>Código de Barras</th>
                <th>Ações</th>
            </tr>
        </thead>
        <tbody>
            <!-- Produtos cadastrados aparecerão aqui -->
        </tbody>
    </table>

    <h2>Adicionar Itens ao Estoque de Usuários</h2>
    <form id="stockForm">
        <select id="stockUserSelect" required>
            <option value="">Selecione um Usuário</option>
            <!-- Usuários aparecerão aqui -->
        </select>
        <input type="text" id="stockProductName" placeholder="Nome do Produto" required>
        <input type="number" id="stockQuantity" placeholder="Quantidade" required>
        <button type="submit">Adicionar ao Estoque</button>
    </form>

    <h2>Gerenciar Estoque de Usuários</h2>
    <form id="manageStockForm">
        <select id="manageUserSelect" required>
            <option value="">Selecione um Usuário</option>
            <!-- Usuários aparecerão aqui -->
        </select>
        <input type="text" id="manageProductName" placeholder="Nome do Produto" required>
        <input type="number" id="manageNewQuantity" placeholder="Nova Quantidade" required>
        <button type="submit">Atualizar Estoque</button>
    </form>

    <h2>Vendas</h2>
    <button id="startSaleBtn">Iniciar Venda</button>
    <button id="viewSalesHistoryBtn">Histórico de Vendas</button>

    <div id="overlay">
        <div class="modal" id="saleScreen" style="display:none;">
            <h2>Venda de Produto</h2>
            <input type="text" id="saleIdentifier" placeholder="Código de Barras ou Nome do Produto" required>
            <input type="number" id="saleQuantity" placeholder="Quantidade" required>
            <button id="addProductToSaleBtn">Adicionar Produto</button>
            <button id="completeSaleBtn" style="display:none;">Finalizar Venda</button>
            <button id="cancelSaleBtn">Cancelar Venda</button>
            <div id="addedProductsList" style="margin-top: 10px;"></div>
            <div id="totalSaleValue" style="margin-top: 10px;"><strong>Valor Total: R$ <span id="totalValue">0.00</span></strong></div>
        </div>

        <div class="modal" id="receiptScreen" style="display:none;">
            <h2>Comprovante de Venda</h2>
            <div id="receiptProducts"></div>
            <p><strong>Valor Total:</strong> R$ <span id="receiptTotalValue"></span></p>
            <p><strong>Valor Pago:</strong> R$ <span id="receiptPayment"></span></p>
            <p><strong>Troco:</strong> R$ <span id="receiptChange"></span></p>
            <p><strong>Data:</strong> <span id="receiptDate"></span></p>
            <button id="printReceiptBtn">Imprimir Comprovante</button>
            <button id="closeReceiptBtn">Fechar</button>
        </div>

        <div class="modal" id="salesHistoryScreen" style="display:none;">
            <h2>Histórico de Vendas</h2>
            <table id="salesHistoryTable">
                <thead>
                    <tr>
                        <th>Produto</th>
                        <th>Quantidade</th>
                        <th>Valor Pago</th>
                        <th>Troco</th>
                        <th>Data</th>
                    </tr>
                </thead>
                <tbody>
                    <!-- Histórico de vendas aparecerá aqui -->
                </tbody>
            </table>
            <button id="closeSalesHistoryBtn">Fechar</button>
        </div>
    </div>
</div>

<script>
    const salesHistory = [];
    const products = [];
    const users = [{ name: "administrador", password: "adm123", stock: [] }];
    let saleItems = [];
    const establishmentInfo = {
        name: "",
        cnpj: "",
        address: "",
        email: "",
        phone: ""
    };

    document.getElementById('loginBtn').addEventListener('click', function() {
        const username = document.getElementById('username').value;
        const password = document.getElementById('password').value;
        const user = users.find(user => user.name === username && user.password === password);

        if (user) {
            document.getElementById('loginScreen').style.display = 'none';
            document.getElementById('mainContent').style.display = 'block';
        } else {
            document.getElementById('loginError').style.display = 'block';
        }
    });

    document.getElementById('establishmentForm').addEventListener('submit', function(e) {
        e.preventDefault();
        establishmentInfo.name = document.getElementById('establishmentName').value;
        establishmentInfo.cnpj = document.getElementById('establishmentCNPJ').value;
        establishmentInfo.address = document.getElementById('establishmentAddress').value;
        establishmentInfo.email = document.getElementById('establishmentEmail').value;
        establishmentInfo.phone = document.getElementById('establishmentPhone').value;
        alert('Informações do estabelecimento salvas com sucesso!');
    });

    document.getElementById('userForm').addEventListener('submit', function(e) {
        e.preventDefault();
        const newUser = {
            name: document.getElementById('userName').value,
            password: document.getElementById('userPassword').value,
            stock: []
        };
        users.push(newUser);
        updateUserTable();
        document.getElementById('userForm').reset();
    });

    function updateUserTable() {
        const tbody = document.getElementById('userTable').getElementsByTagName('tbody')[0];
        tbody.innerHTML = '';
        users.forEach(user => {
            const row = tbody.insertRow();
            row.insertCell(0).textContent = user.name;
            const actionsCell = row.insertCell(1);
            const editBtn = document.createElement('button');
            editBtn.textContent = 'Editar';
            editBtn.onclick = () => editUser(user);
            actionsCell.appendChild(editBtn);
        });
    }

    function editUser(user) {
        document.getElementById('editUserName').value = user.name;
        document.getElementById('editUserPassword').value = user.password;
        document.getElementById('editUserSelect').value = user.name;
        document.getElementById('editUserForm').style.display = 'block';
    }

    document.getElementById('editUserForm').addEventListener('submit', function(e) {
        e.preventDefault();
        const userName = document.getElementById('editUserSelect').value;
        const user = users.find(u => u.name === userName);
        user.name = document.getElementById('editUserName').value;
        user.password = document.getElementById('editUserPassword').value;
        updateUserTable();
        document.getElementById('editUserForm').style.display = 'none';
    });

    document.getElementById('productForm').addEventListener('submit', function(e) {
        e.preventDefault();
        const newProduct = {
            name: document.getElementById('productName').value,
            quantity: parseInt(document.getElementById('productQuantity').value),
            brand: document.getElementById('productBrand').value,
            location: document.getElementById('productLocation').value,
            price: parseFloat(document.getElementById('productPrice').value),
            barcode: document.getElementById('productBarcode').value
        };
        products.push(newProduct);
        updateProductTable();
        document.getElementById('productForm').reset();
    });

    function updateProductTable() {
        const tbody = document.getElementById('productTable').getElementsByTagName('tbody')[0];
        tbody.innerHTML = '';
        products.forEach(product => {
            const row = tbody.insertRow();
            row.insertCell(0).textContent = product.name;
            row.insertCell(1).textContent = product.quantity;
            row.insertCell(2).textContent = product.brand;
            row.insertCell(3).textContent = product.location;
            row.insertCell(4).textContent = `R$ ${product.price.toFixed(2)}`;
            row.insertCell(5).textContent = product.barcode;
            const actionsCell = row.insertCell(6);
            const removeBtn = document.createElement('button');
            removeBtn.textContent = 'Remover';
            removeBtn.onclick = () => removeProduct(product);
            actionsCell.appendChild(removeBtn);
        });
    }

    function removeProduct(product) {
        const index = products.indexOf(product);
        if (index > -1) {
            products.splice(index, 1);
            updateProductTable();
        }
    }

    document.getElementById('startSaleBtn').addEventListener('click', function() {
        document.getElementById('overlay').style.display = 'block';
        document.getElementById('saleScreen').style.display = 'block';
        saleItems = [];
        document.getElementById('addedProductsList').innerHTML = '';
        document.getElementById('totalValue').textContent = '0.00';
        document.getElementById('completeSaleBtn').style.display = 'none';
    });

    document.getElementById('addProductToSaleBtn').addEventListener('click', function() {
        const identifier = document.getElementById('saleIdentifier').value;
        const quantity = parseInt(document.getElementById('saleQuantity').value);
        const product = products.find(p => p.name === identifier || p.barcode === identifier);

        if (product && quantity <= product.quantity) {
            saleItems.push({ product, quantity });
            product.quantity -= quantity; // Atualiza a quantidade no estoque
            updateAddedProductsList();
            document.getElementById('totalValue').textContent = calculateTotal().toFixed(2);
            document.getElementById('saleIdentifier').value = '';
            document.getElementById('saleQuantity').value = '';
            document.getElementById('completeSaleBtn').style.display = 'block';
        } else {
            alert('Produto não encontrado ou quantidade insuficiente.');
        }
    });

    function calculateTotal() {
        return saleItems.reduce((total, item) => total + (item.product.price * item.quantity), 0);
    }

    function updateAddedProductsList() {
        const list = document.getElementById('addedProductsList');
        list.innerHTML = '';
        saleItems.forEach(item => {
            const div = document.createElement('div');
            div.textContent = `${item.product.name} - Quantidade: ${item.quantity} - Preço: R$ ${(item.product.price * item.quantity).toFixed(2)}`;
            list.appendChild(div);
        });
    }

    document.getElementById('completeSaleBtn').addEventListener('click', function() {
        const payment = parseFloat(prompt("Digite o valor pago:"));
        const total = calculateTotal();
        const change = payment - total;

        if (change >= 0) {
            const receiptProducts = saleItems.map(item => `${item.product.name} - Quantidade: ${item.quantity} - Preço: R$ ${(item.product.price * item.quantity).toFixed(2)}`).join('<br>');
            document.getElementById('receiptProducts').innerHTML = receiptProducts;
            document.getElementById('receiptTotalValue').textContent = total.toFixed(2);
            document.getElementById('receiptPayment').textContent = payment.toFixed(2);
            document.getElementById('receiptChange').textContent = change.toFixed(2);
            document.getElementById('receiptDate').textContent = new Date().toLocaleString();
            document.getElementById('saleScreen').style.display = 'none';
            document.getElementById('receiptScreen').style.display = 'block';
            salesHistory.push({ products: saleItems, payment, change, date: new Date() });
        } else {
            alert('Valor pago é insuficiente.');
        }
    });

    document.getElementById('printReceiptBtn').addEventListener('click', function() {
        const printWindow = window.open('', '_blank');
        printWindow.document.write('<html><head><title>Comprovante de Venda</title>');
        printWindow.document.write('<style>body { font-family: Arial, sans-serif; }</style>');
        printWindow.document.write('</head><body>');
        printWindow.document.write('<h2>Comprovante de Venda</h2>');

        printWindow.document.write(`
            <strong>Estabelecimento:</strong> ${establishmentInfo.name}<br>
            <strong>CNPJ:</strong> ${establishmentInfo.cnpj}<br>
            <strong>Endereço:</strong> ${establishmentInfo.address}<br>
            <strong>Email:</strong> ${establishmentInfo.email}<br>
            <strong>Telefone:</strong> ${establishmentInfo.phone}<br><br>
        `);

        saleItems.forEach(item => {
            printWindow.document.write(`${item.product.name} - Quantidade: ${item.quantity} - Preço: R$ ${(item.product.price * item.quantity).toFixed(2)}<br>`);
        });

        printWindow.document.write(`<strong>Valor Total:</strong> R$ ${document.getElementById('receiptTotalValue').textContent}<br>`);
        printWindow.document.write(`<strong>Valor Pago:</strong> R$ ${document.getElementById('receiptPayment').textContent}<br>`);
        printWindow.document.write(`<strong>Troco:</strong> R$ ${document.getElementById('receiptChange').textContent}<br>`);
        printWindow.document.write(`<strong>Data:</strong> ${document.getElementById('receiptDate').textContent}<br>`);
        printWindow.document.write('</body></html>');
        printWindow.document.close();
        printWindow.print();
    });

    document.getElementById('cancelSaleBtn').addEventListener('click', function() {
        document.getElementById('saleScreen').style.display = 'none';
        document.getElementById('overlay').style.display = 'none';
    });

    document.getElementById('viewSalesHistoryBtn').addEventListener('click', function() {
        const tbody = document.getElementById('salesHistoryTable').getElementsByTagName('tbody')[0];
        tbody.innerHTML = '';
        salesHistory.forEach(sale => {
            sale.products.forEach(item => {
                const row = tbody.insertRow();
                row.insertCell(0).textContent = item.product.name;
                row.insertCell(1).textContent = item.quantity;
                row.insertCell(2).textContent = item.payment.toFixed(2);
                row.insertCell(3).textContent = item.change.toFixed(2);
                row.insertCell(4).textContent = sale.date.toLocaleString();
            });
        });
        document.getElementById('salesHistoryScreen').style.display = 'block';
    });

    document.getElementById('closeReceiptBtn').addEventListener('click', function() {
        document.getElementById('receiptScreen').style.display = 'none';
        document.getElementById('overlay').style.display = 'none';
    });

    document.getElementById('closeSalesHistoryBtn').addEventListener('click', function() {
        document.getElementById('salesHistoryScreen').style.display = 'none';
        document.getElementById('overlay').style.display = 'none';
    });
</script>

</body>
</html>
