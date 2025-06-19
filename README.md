<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BAZAR SERDANI - نظام إدارة المخزون</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #f0f2f5;
            margin: 0;
            padding: 0;
            color: #333;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            background-color: #fff;
            min-height: 100vh;
            box-shadow: 0 0 15px rgba(0,0,0,0.1);
        }
        
        header {
            background-color: #2c3e50;
            color: white;
            padding: 15px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        
        .logo {
            font-weight: bold;
            font-size: 20px;
        }
        
        .nav-menu {
            display: flex;
            background-color: #34495e;
        }
        
        .nav-menu a {
            color: white;
            text-decoration: none;
            padding: 15px 20px;
            display: block;
            transition: background-color 0.3s;
        }
        
        .nav-menu a:hover {
            background-color: #3d566e;
        }
        
        .main-content {
            padding: 20px;
        }
        
        .section {
            display: none;
            margin-bottom: 30px;
        }
        
        .section.active {
            display: block;
        }
        
        .section-title {
            color: #2c3e50;
            border-bottom: 2px solid #3498db;
            padding-bottom: 10px;
            margin-top: 0;
        }
        
        .form-group {
            margin-bottom: 15px;
        }
        
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        
        input, select {
            width: 100%;
            padding: 8px 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            box-sizing: border-box;
            font-size: 16px;
        }
        
        button {
            background-color: #3498db;
            color: white;
            border: none;
            padding: 10px 15px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
            transition: background-color 0.3s;
            margin: 5px;
        }
        
        button:hover {
            background-color: #2980b9;
        }
        
        /* تصميم الباركود */
        .barcode-label {
            width: 30mm;
            height: 20mm;
            display: inline-flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            border: 1px dashed #ccc;
            margin: 5px;
            padding: 2px;
            text-align: center;
            font-size: 10px;
        }
        
        .barcode-img {
            max-width: 28mm;
            max-height: 15mm;
            object-fit: contain;
        }
        
        .barcode-container {
            display: flex;
            flex-wrap: wrap;
            margin-top: 20px;
        }
        
        /* جدول المنتجات */
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        
        th, td {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: center;
        }
        
        th {
            background-color: #f2f2f2;
        }
        
        /* تعديلات الطباعة */
        @media print {
            body * {
                visibility: hidden;
            }
            .barcode-section, .barcode-section * {
                visibility: visible;
            }
            .barcode-section {
                position: absolute;
                top: 0;
                left: 0;
                width: 100%;
            }
            .no-print {
                display: none;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <div class="logo">BAZAR SERDANI</div>
            <div class="current-page" id="current-page">الصفحة الرئيسية</div>
        </header>
        
        <nav class="nav-menu">
            <a href="#" data-section="home">الصفحة الرئيسية</a>
            <a href="#" data-section="barcode">طباعة الباركود</a>
            <a href="#" data-section="add-product">إضافة منتج</a>
            <a href="#" data-section="inventory">المخزون</a>
            <a href="#" data-section="sales">المبيعات</a>
            <a href="#" data-section="debts">الديون</a>
        </nav>
        
        <div class="main-content">
            <!-- قسم الصفحة الرئيسية -->
            <section id="home" class="section active">
                <h2 class="section-title">الصفحة الرئيسية</h2>
                <p>مرحبًا بك في نظام إدارة متجر BAZAR SERDANI</p>
                <div class="stats">
                    <div>عدد المنتجات: <span id="products-count">0</span></div>
                    <div>إجمالي المبيعات اليوم: <span id="today-sales">0</span> دج</div>
                </div>
            </section>
            
            <!-- قسم طباعة الباركود -->
            <section id="barcode" class="section">
                <h2 class="section-title">طباعة الباركود</h2>
                
                <div class="form-group">
                    <label for="product-select">اختر المنتج:</label>
                    <select id="product-select">
                        <option value="">-- اختر منتج --</option>
                    </select>
                </div>
                
                <div class="form-group">
                    <label for="copy-count">عدد النسخ:</label>
                    <input type="number" id="copy-count" value="1" min="1">
                </div>
                
                <button id="generate-btn">توليد طباعة</button>
                <button id="print-btn" class="no-print">طباعة</button>
                <button id="clear-btn" class="no-print">مسح</button>
                
                <div id="barcode-container" class="barcode-container"></div>
            </section>
            
            <!-- قسم إضافة منتج -->
            <section id="add-product" class="section">
                <h2 class="section-title">إضافة منتج</h2>
                
                <div class="form-group">
                    <label for="product-id">رقم المنتج:</label>
                    <input type="text" id="product-id" placeholder="أدخل رقم المنتج">
                </div>
                
                <div class="form-group">
                    <label for="product-name">اسم المنتج:</label>
                    <input type="text" id="product-name" placeholder="أدخل اسم المنتج">
                </div>
                
                <div class="form-group">
                    <label for="product-price">السعر (دج):</label>
                    <input type="number" id="product-price" placeholder="أدخل السعر">
                </div>
                
                <div class="form-group">
                    <label for="product-quantity">الكمية:</label>
                    <input type="number" id="product-quantity" value="1" min="1">
                </div>
                
                <button id="save-product">حفظ المنتج</button>
            </section>
            
            <!-- قسم المخزون -->
            <section id="inventory" class="section">
                <h2 class="section-title">المخزون</h2>
                <div class="form-group">
                    <input type="text" id="inventory-search" placeholder="ابحث عن منتج...">
                </div>
                <table id="inventory-table">
                    <thead>
                        <tr>
                            <th>رقم المنتج</th>
                            <th>اسم المنتج</th>
                            <th>السعر</th>
                            <th>الكمية</th>
                            <th>تعديل</th>
                        </tr>
                    </thead>
                    <tbody>
                        <!-- سيتم ملؤه بالبيانات -->
                    </tbody>
                </table>
            </section>
            
            <!-- قسم المبيعات -->
            <section id="sales" class="section">
                <h2 class="section-title">المبيعات</h2>
                <div id="sales-stats">
                    <div>إجمالي المبيعات اليوم: <span id="daily-sales">0</span> دج</div>
                    <div>إجمالي المبيعات الشهرية: <span id="monthly-sales">0</span> دج</div>
                </div>
                <table id="sales-table">
                    <thead>
                        <tr>
                            <th>التاريخ</th>
                            <th>رقم الفاتورة</th>
                            <th>المبلغ</th>
                            <th>التفاصيل</th>
                        </tr>
                    </thead>
                    <tbody>
                        <!-- سيتم ملؤه بالبيانات -->
                    </tbody>
                </table>
            </section>
            
            <!-- قسم الديون -->
            <section id="debts" class="section">
                <h2 class="section-title">الديون</h2>
                <div class="form-group">
                    <input type="text" id="debt-search" placeholder="ابحث عن عميل...">
                </div>
                <table id="debts-table">
                    <thead>
                        <tr>
                            <th>اسم العميل</th>
                            <th>المبلغ</th>
                            <th>التاريخ</th>
                            <th>الحالة</th>
                        </tr>
                    </thead>
                    <tbody>
                        <!-- سيتم ملؤه بالبيانات -->
                    </tbody>
                </table>
            </section>
        </div>
    </div>

    <script>
        // بيانات التطبيق
        let products = [
            { id: '001', name: 'Gandora', price: 1000, quantity: 10 },
            { id: '002', name: 'منتج 2', price: 1500, quantity: 5 },
            { id: '003', name: 'منتج 3', price: 2000, quantity: 8 }
        ];
        
        let sales = [];
        let debts = [];
        
        // تهيئة التطبيق عند التحميل
        document.addEventListener('DOMContentLoaded', function() {
            loadProducts();
            loadSales();
            loadDebts();
            updateStats();
            setupNavigation();
        });
        
        // تكوين نظام التنقل بين الأقسام
        function setupNavigation() {
            const navLinks = document.querySelectorAll('.nav-menu a');
            
            navLinks.forEach(link => {
                link.addEventListener('click', function(e) {
                    e.preventDefault();
                    const sectionId = this.getAttribute('data-section');
                    showSection(sectionId);
                    
                    // تحديث عنوان الصفحة الحالية
                    document.getElementById('current-page').textContent = this.textContent;
                });
            });
        }
        
        // عرض القسم المحدد وإخفاء الآخرين
        function showSection(sectionId) {
            const sections = document.querySelectorAll('.section');
            sections.forEach(section => {
                section.classList.remove('active');
            });
            
            document.getElementById(sectionId).classList.add('active');
        }
        
        // تحميل المنتجات في القائمة المنسدلة
        function loadProducts() {
            const select = document.getElementById('product-select');
            select.innerHTML = '<option value="">-- اختر منتج --</option>';
            
            products.forEach(product => {
                const option = document.createElement('option');
                option.value = product.id;
                option.textContent = `${product.id} - ${product.name}`;
                select.appendChild(option);
            });
            
            // تحديث جدول المخزون
            updateInventoryTable();
            
            // تحديث الإحصاءات
            document.getElementById('products-count').textContent = products.length;
        }
        
        // تحديث جدول المخزون
        function updateInventoryTable() {
            const tbody = document.querySelector('#inventory-table tbody');
            tbody.innerHTML = '';
            
            products.forEach(product => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${product.id}</td>
                    <td>${product.name}</td>
                    <td>${product.price} دج</td>
                    <td>${product.quantity}</td>
                    <td><button class="edit-product" data-id="${product.id}">تعديل</button></td>
                `;
                tbody.appendChild(row);
            });
        }
        
        // توليد الباركود
        document.getElementById('generate-btn').addEventListener('click', function() {
            const productId = document.getElementById('product-select').value;
            const copies = parseInt(document.getElementById('copy-count').value);
            
            if (!productId || copies < 1) {
                alert('الرجاء اختيار منتج وإدخال عدد النسخ');
                return;
            }
            
            const product = products.find(p => p.id === productId);
            if (!product) return;
            
            const container = document.getElementById('barcode-container');
            container.innerHTML = '';
            
            for (let i = 0; i < copies; i++) {
                const barcodeDiv = document.createElement('div');
                barcodeDiv.className = 'barcode-label';
                barcodeDiv.innerHTML = `
                    <div>BAZAR SERDANI</div>
                    <img src="https://barcode.tec-it.com/barcode.ashx?data=${productId}&code=Code128&dpi=96" 
                         class="barcode-img"
                         alt="باركود ${product.name}">
                    <div>Prix: ${product.price} DA</div>
                    <div>${productId}</div>
                `;
                container.appendChild(barcodeDiv);
            }
        });
        
        // طباعة الباركود
        document.getElementById('print-btn').addEventListener('click', function() {
            window.print();
        });
        
        // مسح الباركود
        document.getElementById('clear-btn').addEventListener('click', function() {
            document.getElementById('barcode-container').innerHTML = '';
        });
        
        // إضافة منتج جديد
        document.getElementById('save-product').addEventListener('click', function() {
            const id = document.getElementById('product-id').value;
            const name = document.getElementById('product-name').value;
            const price = parseFloat(document.getElementById('product-price').value);
            const quantity = parseInt(document.getElementById('product-quantity').value);
            
            if (!id || !name || isNaN(price) || isNaN(quantity)) {
                alert('الرجاء ملء جميع الحقول بشكل صحيح');
                return;
            }
            
            // التحقق من عدم وجود منتج بنفس الرقم
            if (products.some(p => p.id === id)) {
                alert('رقم المنتج موجود مسبقاً');
                return;
            }
            
            const newProduct = { id, name, price, quantity };
            products.push(newProduct);
            
            // تحديث الواجهة
            loadProducts();
            
            // مسح حقول الإدخال
            document.getElementById('product-id').value = '';
            document.getElementById('product-name').value = '';
            document.getElementById('product-price').value = '';
            document.getElementById('product-quantity').value = '1';
            
            alert('تم إضافة المنتج بنجاح');
        });
        
        // تحميل بيانات المبيعات
        function loadSales() {
            // هنا يمكن جلب البيانات من قاعدة البيانات أو API
            sales = [
                { date: '2023-05-01', invoice: 'INV001', amount: 4500, details: 'Gandora x3' },
                { date: '2023-05-02', invoice: 'INV002', amount: 3000, details: 'منتج 2 x2' }
            ];
            
            updateSalesTable();
        }
        
        // تحديث جدول المبيعات
        function updateSalesTable() {
            const tbody = document.querySelector('#sales-table tbody');
            tbody.innerHTML = '';
            
            sales.forEach(sale => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${sale.date}</td>
                    <td>${sale.invoice}</td>
                    <td>${sale.amount} دج</td>
                    <td>${sale.details}</td>
                `;
                tbody.appendChild(row);
            });
            
            // حساب إجمالي المبيعات
            const dailyTotal = sales.reduce((sum, sale) => sum + sale.amount, 0);
            document.getElementById('daily-sales').textContent = dailyTotal;
            document.getElementById('today-sales').textContent = dailyTotal;
            document.getElementById('monthly-sales').textContent = dailyTotal * 30; // مثال فقط
        }
        
        // تحميل بيانات الديون
        function loadDebts() {
            // هنا يمكن جلب البيانات من قاعدة البيانات أو API
            debts = [
                { customer: 'عميل 1', amount: 2000, date: '2023-04-15', status: 'غير مسدد' },
                { customer: 'عميل 2', amount: 1500, date: '2023-04-20', status: 'مسدد جزئياً' }
            ];
            
            updateDebtsTable();
        }
        
        // تحديث جدول الديون
        function updateDebtsTable() {
            const tbody = document.querySelector('#debts-table tbody');
            tbody.innerHTML = '';
            
            debts.forEach(debt => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${debt.customer}</td>
                    <td>${debt.amount} دج</td>
                    <td>${debt.date}</td>
                    <td>${debt.status}</td>
                `;
                tbody.appendChild(row);
            });
        }
        
        // تحديث الإحصاءات
        function updateStats() {
            document.getElementById('products-count').textContent = products.length;
            
            const dailyTotal = sales.reduce((sum, sale) => sum + sale.amount, 0);
            document.getElementById('today-sales').textContent = dailyTotal;
        }
    </script>
</body>
</html>
