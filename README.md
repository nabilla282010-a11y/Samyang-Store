# Samyang-Store
Nabilla samyang store
<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Nabilla Samyang Store</title>
<style>
body {font-family:'Segoe UI'; background:linear-gradient(135deg,#ff6b6b,#ee5a6f); color:#333; margin:0;}
header,footer {background:rgba(0,0,0,0.9); color:white; text-align:center; padding:15px;}
.container {max-width:1200px; margin:auto; padding:20px;}
button {cursor:pointer;}
.product-grid {display:grid;grid-template-columns:repeat(auto-fit,minmax(280px,1fr));gap:25px;}
.product-card {background:white;padding:20px;border-radius:15px;text-align:center;box-shadow:0 6px 10px rgba(0,0,0,0.2);}
.add-to-cart {background:#ff6b6b;color:white;border:none;padding:10px 20px;border-radius:8px;font-weight:bold;}
.add-to-cart:hover {background:#ff5252;}
.modal {display:none;position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.7);align-items:center;justify-content:center;z-index:100;}
.modal-content {background:white;padding:20px;border-radius:15px;width:90%;max-width:600px;}
.close {background:none;border:none;font-size:24px;cursor:pointer;float:right;}
.cart-item {display:flex;justify-content:space-between;padding:10px 0;border-bottom:1px solid #eee;}
.cart-total {text-align:right;font-weight:bold;margin-top:15px;}
.checkout-btn,.confirm-btn {width:100%;background:#4CAF50;color:white;border:none;padding:12px;border-radius:8px;margin-top:20px;font-weight:bold;}
.form-group {margin-bottom:15px;}
label {display:block;font-weight:bold;margin-bottom:5px;}
input,select {width:100%;padding:10px;border:1px solid #ccc;border-radius:6px;}
.nota {background:white;border-radius:15px;padding:25px;max-width:600px;margin:40px auto;box-shadow:0 6px 10px rgba(0,0,0,0.2);}
.whatsapp-btn {background:#25D366;color:white;border:none;padding:12px;width:100%;border-radius:8px;margin-top:10px;font-weight:bold;}
</style>
</head>
<body>

<header>
    <h1>🔥 Nabilla Samyang Store</h1>
</header>

<div class="container">
    <h2 style="text-align:center;color:white;">Belanja Mie Pedas Favoritmu 🌶️</h2>
    <div class="product-grid" id="productGrid"></div>
    <button class="add-to-cart" style="margin-top:30px;display:block;margin:auto;" onclick="openCart()">🛒 Lihat Keranjang (<span id="cartCount">0</span>)</button>
</div>

<!-- MODAL KERANJANG -->
<div class="modal" id="cartModal">
    <div class="modal-content">
        <button class="close" onclick="closeCart()">✖</button>
        <h2>Keranjang Belanja</h2>
        <div id="cartItems"></div>
        <div class="cart-total">Total: Rp <span id="totalPrice">0</span></div>
        <button class="checkout-btn" onclick="openCheckout()">Lanjut ke Pembayaran</button>
    </div>
</div>

<!-- MODAL CHECKOUT -->
<div class="modal" id="checkoutModal">
    <div class="modal-content">
        <button class="close" onclick="closeCheckout()">✖</button>
        <h2>Form Pembelian</h2>
        <div class="form-group">
            <label>Nama:</label>
            <input type="text" id="buyerName">
        </div>
        <div class="form-group">
            <label>Alamat:</label>
            <input type="text" id="buyerAddress">
        </div>
        <div class="form-group">
            <label>Metode Pembayaran:</label>
            <select id="paymentMethod">
                <option value="">-- Pilih --</option>
                <option>COD (Bayar di Tempat)</option>
                <option>Transfer Bank</option>
                <option>E-Wallet</option>
            </select>
        </div>
        <button class="confirm-btn" onclick="confirmPurchase()">Konfirmasi</button>
    </div>
</div>

<div id="notaContainer"></div>

<footer>
    <p>&copy; 2025 Nabilla Foods | Pedas itu Nikmat 🔥</p>
</footer>

<script>
const products = [
    {id:1,name:"Samyang Original",price:15000,emoji:"🍜"},
    {id:2,name:"Samyang 2x Spicy",price:18000,emoji:"🔥"},
    {id:3,name:"Samyang Carbonara",price:17000,emoji:"🧀"},
    {id:4,name:"Samyang Cheese",price:16000,emoji:"🧈"},
];
let cart=[];

function renderProducts(){
    const grid=document.getElementById('productGrid');
    grid.innerHTML=products.map(p=>`
        <div class="product-card">
            <div style="font-size:60px;">${p.emoji}</div>
            <h3>${p.name}</h3>
            <p>Rp ${p.price.toLocaleString('id-ID')}</p>
            <button class="add-to-cart" onclick="addToCart(${p.id})">Tambah ke Keranjang</button>
        </div>
    `).join('');
}
renderProducts();

function addToCart(id){
    const p=products.find(pr=>pr.id===id);
    const exist=cart.find(c=>c.id===id);
    if(exist) exist.qty++;
    else cart.push({...p,qty:1});
    updateCart();
    alert(`${p.name} ditambahkan ke keranjang!`);
}

function updateCart(){
    document.getElementById('cartCount').textContent=cart.reduce((a,b)=>a+b.qty,0);
    const items=document.getElementById('cartItems');
    if(cart.length===0){items.innerHTML='<p>Keranjang kosong.</p>';return;}
    items.innerHTML=cart.map(i=>`
        <div class="cart-item">
            <div><b>${i.name}</b><br>Rp ${i.price.toLocaleString('id-ID')} x ${i.qty}</div>
            <div>
                <button onclick="changeQty(${i.id},-1)">-</button>
                <button onclick="changeQty(${i.id},1)">+</button>
            </div>
        </div>
    `).join('');
    const total=cart.reduce((s,i)=>s+i.price*i.qty,0);
    document.getElementById('totalPrice').textContent=total.toLocaleString('id-ID');
}

function changeQty(id,delta){
    const item=cart.find(i=>i.id===id);
    if(!item)return;
    item.qty+=delta;
    if(item.qty<=0) cart=cart.filter(i=>i.id!==id);
    updateCart();
}
function openCart(){document.getElementById('cartModal').style.display='flex';}
function closeCart(){document.getElementById('cartModal').style.display='none';}
function openCheckout(){
    if(cart.length===0){alert('Keranjang kosong!');return;}
    closeCart(); document.getElementById('checkoutModal').style.display='flex';
}
function closeCheckout(){document.getElementById('checkoutModal').style.display='none';}

function confirmPurchase(){
    const name=document.getElementById('buyerName').value.trim();
    const address=document.getElementById('buyerAddress').value.trim();
    const pay=document.getElementById('paymentMethod').value;
    if(!name||!address||!pay){alert('Lengkapi semua data!');return;}

    const total=cart.reduce((s,i)=>s+i.price*i.qty,0);
    const detail=cart.map(i=>`${i.name} (${i.qty}x) - Rp ${(i.price*i.qty).toLocaleString('id-ID')}`).join('%0A');

    // nomor admin / grup (ganti ke nomor kamu)
    const adminPhone = "6281234567890"; // ubah ke nomor WA tujuan (gunakan 62)
    const pesan =
`Halo Nabilla Store!%0A
Saya ingin melakukan pemesanan:%0A
=====================%0A
${detail}%0A
=====================%0A
Total: Rp ${total.toLocaleString('id-ID')}%0A
Nama: ${name}%0A
Alamat: ${address}%0A
Pembayaran: ${pay}%0A
Terima kasih!`;

    const waLink=`https://wa.me/${adminPhone}?text=${pesan}`;

    // tampilkan nota dan tombol kirim
    document.getElementById('notaContainer').innerHTML=`
        <div class="nota">
            <h2>🧾 Nota Pembelian</h2><hr>
            <p><b>Nama:</b> ${name}</p>
            <p><b>Alamat:</b> ${address}</p>
            <p><b>Pembayaran:</b> ${pay}</p>
            <ul>${cart.map(i=>`<li>${i.name} (${i.qty}x)</li>`).join('')}</ul>
            <p><b>Total: Rp ${total.toLocaleString('id-ID')}</b></p>
            <button class="whatsapp-btn" onclick="window.open('${waLink}','_blank')">📱 Kirim ke WhatsApp</button>
        </div>
    `;
    closeCheckout();
    cart=[]; updateCart();
}
</script>
</body>
</html>
