# Eat n Split 🍽️

Aplikasi React untuk mengelola teman dan membagi biaya tagihan dengan mudah. Aplikasi ini membantu Anda melacak siapa yang berhutang uang kepada siapa dalam setiap transaksi yang dibagi.

## Deskripsi Project

**Eat n Split** adalah aplikasi web interaktif yang memungkinkan pengguna untuk:
- ✅ Melihat daftar teman dengan saldo hutang atau piutang
- ✅ Menambah teman baru ke dalam aplikasi
- ✅ Membagi biaya tagihan dengan teman yang dipilih
- ✅ Melacak siapa yang berhutang kepada siapa dalam setiap transaksi

Aplikasi ini dirancang dengan antarmuka yang sederhana dan responsif, menggunakan React hooks untuk state management. Setiap transaksi pembayaran akan otomatis memperbarui saldo teman yang dipilih.

---

## Struktur Project

```
src/
├── index.js          # File entry point aplikasi
├── App.js            # Main component dan logika aplikasi
└── index.css         # Styling untuk seluruh aplikasi
```

---

## Penjelasan Kode Line by Line

### 1. **src/index.js** - Entry Point

```javascript
import React from "react";
import ReactDOM from "react-dom/client";
import "./index.css";
import App from "./App";
```
- **Baris 1-4**: Mengimpor library React, ReactDOM, CSS styling, dan komponen App utama
- React: Library untuk membuat UI dengan komponen
- ReactDOM: Library untuk render React component ke DOM
- "./index.css": File styling untuk keseluruhan aplikasi
- App: Komponen utama aplikasi

```javascript
const root = ReactDOM.createRoot(document.getElementById("root"));
```
- **Baris 6**: Membuat root React element dengan cara baru (React 18+)
- Mencari elemen HTML dengan id "root" di file index.html
- Menyimpannya dalam variabel `root` untuk digunakan merender aplikasi

```javascript
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
);
```
- **Baris 7-11**: Merender komponen App ke dalam root element
- `<React.StrictMode>`: Wrapper yang membantu mengidentifikasi potensi masalah dalam aplikasi
- `<App />`: Komponen utama aplikasi

---

### 2. **src/App.js** - Komponen Utama

#### **Bagian 1: Inisialisasi Data dan Import**

```javascript
import { useState } from "react";
```
- **Baris 1**: Mengimpor hook `useState` dari React
- Hook ini digunakan untuk mengelola state (data dinamis) dalam functional component

```javascript
const initialFriends = [
  {
    id: 118836,
    name: "Clark",
    image: "https://i.pravatar.cc/48?u=118836",
    balance: -7,
  },
  {
    id: 933372,
    name: "Sarah",
    image: "https://i.pravatar.cc/48?u=933372",
    balance: 20,
  },
  {
    id: 499476,
    name: "Anthony",
    image: "https://i.pravatar.cc/48?u=499476",
    balance: 0,
  },
];
```
- **Baris 3-22**: Array data teman awal dengan struktur:
  - `id`: Identifier unik untuk setiap teman (type number)
  - `name`: Nama teman (string)
  - `image`: URL avatar dari gravatar (string)
  - `balance`: Saldo transaksi dengan teman
    - Nilai negatif: User berhutang kepada teman
    - Nilai positif: Teman berhutang kepada user
    - Nilai 0: Transaksi selesai/tidak ada hutang

#### **Bagian 2: Komponen App (Main Component)**

```javascript
export default function App() {
```
- **Baris 24**: Mendefinisikan dan export default komponen App
- Ini adalah komponen utama yang akan dirender ke root

```javascript
  const [friends, setFriends] = useState(initialFriends);
```
- **Baris 25**: State untuk mengelola daftar teman
- `friends`: Nilai state saat ini (array of friends)
- `setFriends`: Fungsi untuk mengupdate state
- `useState(initialFriends)`: Inisialisasi dengan data teman awal

```javascript
  const [showAddFriend, setShowAddFriend] = useState(false);
```
- **Baris 26**: State boolean untuk menampilkan/menyembunyikan form tambah teman
- Awalnya false (form tersembunyi)

```javascript
  const [selectedFriend, setSelectedFriend] = useState(null);
```
- **Baris 27**: State untuk menyimpan teman yang dipilih
- Awalnya null (tidak ada teman yang dipilih)
- Akan digunakan untuk menampilkan form split bill

#### **Bagian 3: Event Handler Functions**

```javascript
  function handleShowAddFriend(e) {
    e.preventDefault();
    setShowAddFriend(!showAddFriend);
  }
```
- **Baris 29-32**: Handler untuk tombol "Add Friend"
- `e.preventDefault()`: Mencegah default behavior form submit
- `setShowAddFriend(!showAddFriend)`: Toggle tampilan form (true jadi false, false jadi true)

```javascript
  function handleAddFriend(friend) {
    setFriends((friends) => [...friends, friend]);
    setShowAddFriend(false);
  }
```
- **Baris 34-37**: Handler ketika user menambah teman baru
- `setFriends((friends) => [...friends, friend])`: Spread operator untuk add teman baru
  - `...friends`: Spread semua teman lama
  - Diikuti dengan `friend` object baru
  - Ini membuat array baru (immutable update)
- `setShowAddFriend(false)`: Menyembunyikan form setelah submit

```javascript
  function handleSelectFriend(friend) {
    setSelectedFriend((selected) =>
      selected?.id === friend.id ? null : friend,
    );
    setShowAddFriend(false);
  }
```
- **Baris 39-45**: Handler ketika user memilih teman
- `selected?.id === friend.id ? null : friend`: Toggle selection
  - `?.` adalah optional chaining (aman jika selected null)
  - Jika teman yang diklik sama dengan teman yang dipilih → set null (deselect)
  - Jika berbeda atau none → set teman baru sebagai selected
- `setShowAddFriend(false)`: Sembunyikan form add friend

```javascript
  function handleSplitBill(value) {
    setFriends((friends) =>
      friends.map((friend) => {
        if (friend.id === selectedFriend.id) {
          return {
            ...friend,
            balance: friend.balance + value,
          };
        }
        return friend;
      }),
    );
    setSelectedFriend(null);
  }
```
- **Baris 47-60**: Handler ketika user membagi tagihan
- `setFriends((friends) => friends.map(...))`: Update list teman
- `map()`: Iterasi setiap teman untuk menemukan yang dipilih
- `if (friend.id === selectedFriend.id)`: Cek apakah teman ini yang dipilih
- Jika iya:
  - `...friend`: Spread semua property teman
  - `balance: friend.balance + value`: Update balance dengan nilai split bill
- Jika tidak: `return friend` tanpa perubahan
- `setSelectedFriend(null)`: Deselect teman setelah split bill

#### **Bagian 4: Render UI**

```javascript
  return (
    <div className="app">
      <div className="sidebar">
```
- **Baris 62-64**: Root div dan sidebar container
- `className="app"`: Styling dari CSS (2-column grid layout)
- `className="sidebar"`: Styling untuk panel kiri

```javascript
        <FriendLists
          friends={friends}
          selectedFriend={selectedFriend}
          onSelectFriend={handleSelectFriend}
        />
```
- **Baris 65-69**: Render komponen FriendLists
- `friends={friends}`: Pass state teman ke child component
- `selectedFriend={selectedFriend}`: Pass teman yang dipilih
- `onSelectFriend={handleSelectFriend}`: Pass handler function

```javascript
        {showAddFriend && <FormAddFriend onAddFriend={handleAddFriend} />}
```
- **Baris 70**: Conditional rendering
- `showAddFriend &&`: Hanya render jika showAddFriend true
- `<FormAddFriend ...>`: Render form jika kondisi true
- `onAddFriend={handleAddFriend}`: Pass handler untuk submit form

```javascript
        <Button onClick={handleShowAddFriend}>
          {showAddFriend ? "Close" : "Add Friend"}
        </Button>
```
- **Baris 71-73**: Button toggle untuk show/hide form
- `onClick={handleShowAddFriend}`: Attach handler
- Ternary operator untuk text button berdasarkan state

```javascript
      {selectedFriend && (
        <FormSplitBill
          selectedFriend={selectedFriend}
          onSplitBill={handleSplitBill}
        />
      )}
```
- **Baris 75-80**: Conditional rendering untuk form split bill
- Hanya muncul jika ada teman yang dipilih (selectedFriend !== null)
- Pass teman yang dipilih dan handler function

---

#### **Bagian 5: Sub-Components**

```javascript
function FriendLists({ friends, selectedFriend, onSelectFriend }) {
  return (
    <ul>
      {friends.map((friend) => (
        <Friend
          key={friend.id}
          friend={friend}
          selectedFriend={selectedFriend}
          onSelectFriend={onSelectFriend}
        />
      ))}
    </ul>
  );
}
```
- **Baris 85-98**: Komponen untuk render daftar teman
- Props: `friends`, `selectedFriend`, `onSelectFriend`
- `friends.map()`: Iterasi setiap teman dan render komponen `<Friend>`
- `key={friend.id}`: React key untuk optimasi render (harus unique)
- Setiap `<Friend>` dapat diklik untuk select/deselect

```javascript
function Friend({ friend, selectedFriend, onSelectFriend }) {
  const isSelected = friend.id === selectedFriend?.id;
```
- **Baris 100-101**: Komponen untuk render single teman
- `isSelected`: Boolean untuk cek apakah teman ini sedang dipilih
- `selectedFriend?.id`: Optional chaining (aman jika null)

```javascript
  return (
    <li className={isSelected ? "selected" : ""}>
      <img src={friend.image} alt={friend.name} />
      <h3>{friend.name}</h3>
```
- **Baris 103-106**: Render teman dalam `<li>` element
- `className={isSelected ? "selected" : ""}`: Conditional class untuk styling
- Jika selected → class "selected" (highlight background)
- Jika tidak → class kosong
- `<img>`: Avatar teman dari URL
- `<h3>`: Nama teman

```javascript
      {friend.balance < 0 && (
        <p className="red">
          You owe {friend.name} {Math.abs(friend.balance)}€
        </p>
      )}
      {friend.balance > 0 && (
        <p className="green">
          {friend.name} owes you {Math.abs(friend.balance)}€
        </p>
      )}
      {friend.balance === 0 && <p>You and {friend.name} are even.</p>}
```
- **Baris 108-118**: Render status saldo dengan conditional rendering
- `balance < 0` (RED): User berhutang kepada teman
  - Tampilkan "You owe [name] [amount]€"
  - `Math.abs()`: Convert negatif ke positif untuk display
- `balance > 0` (GREEN): Teman berhutang kepada user
  - Tampilkan "[name] owes you [amount]€"
- `balance === 0` (NEUTRAL): Transaksi selesai
  - Tampilkan "You and [name] are even."

```javascript
      <Button onClick={() => onSelectFriend(friend)}>
        {isSelected ? "Close" : "Select"}
      </Button>
    </li>
  );
}
```
- **Baris 120-124**: Button select/deselect teman
- `onClick={() => onSelectFriend(friend)}`: Arrow function untuk pass teman ke handler
- Text berubah berdasarkan `isSelected` state

```javascript
function Button({ children, onClick }) {
  return (
    <button className="button" onClick={onClick}>
      {children}
    </button>
  );
}
```
- **Baris 127-133**: Reusable Button component
- `children`: Text/content dalam button
- Props: `children`, `onClick`
- Styling dari class "button" di CSS

```javascript
function FormAddFriend({ onAddFriend }) {
  const [name, setName] = useState("");
  const [image, setImage] = useState("https://i.pravatar.cc/48?u=");
```
- **Baris 135-137**: Komponen form untuk tambah teman
- State untuk name dan image URL input

```javascript
  function handleSubmit(e) {
    e.preventDefault();
    const id = crypto.randomUUID();

    if (!name || !image) return;
    const newFriend = {
      id,
      name,
      image: `${image}${id}`,
      balance: 0,
    };
    onAddFriend(newFriend);
    setName("");
    setImage("https://i.pravatar.cc/48?u=");
  }
```
- **Baris 139-153**: Handler submit form tambah teman
- `e.preventDefault()`: Prevent form default submit behavior
- `crypto.randomUUID()`: Generate unique ID untuk teman baru
- `if (!name || !image) return`: Validasi input (exit jika kosong)
- `newFriend` object: Struktur data teman baru
  - `id`: UUID yang di-generate
  - `name`: Dari input
  - `image`: URL + ID (untuk unique avatar)
  - `balance`: 0 (transaksi baru, belum ada hutang)
- `onAddFriend(newFriend)`: Kirim teman baru ke parent component
- Reset input fields setelah submit

```javascript
  return (
    <form className="form-add-friend" onSubmit={handleSubmit}>
      <label>Friend Name</label>
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
      />

      <label>Image URL</label>
      <input
        type="text"
        value={image}
        onChange={(e) => setImage(e.target.value)}
      />

      <Button>Add Friend</Button>
    </form>
  );
}
```
- **Baris 155-173**: Render form untuk input teman baru
- 2 input fields: name dan image URL
- Controlled inputs:
  - `value={name}`: Bind ke state
  - `onChange={(e) => setName(e.target.value)}`: Update state saat user ketik
- `onSubmit={handleSubmit}`: Handler untuk submit button
- `<Button>`: Custom button component

```javascript
function FormSplitBill({ selectedFriend, onSplitBill }) {
  const [bill, setBill] = useState("");
  const [paidByUser, setPaidByUser] = useState("");
  const paidByFriend = bill ? bill - paidByUser : "";
  const [whoIsPaying, setWhoIsPaying] = useState("me");
```
- **Baris 176-180**: Komponen form untuk split bill
- State:
  - `bill`: Total tagihan
  - `paidByUser`: Jumlah yang dibayar user
  - `paidByFriend`: Calculated (total - user) - TIDAK state, computed
  - `whoIsPaying`: Select siapa yang bayar ("me" atau "friend")

```javascript
  function handleSubmit(e) {
    e.preventDefault();

    if (!bill || !paidByUser) return;
    onSplitBill(whoIsPaying === "me" ? paidByUser : -paidByUser);
  }
```
- **Baris 182-187**: Handler submit form split bill
- Validasi input bill dan paidByUser
- `whoIsPaying === "me" ? paidByUser : -paidByUser`:
  - Jika user bayar: pass positif (user memberikan uang)
  - Jika friend bayar: pass negatif (user berhutang)
- Value ini akan di-add ke balance teman di parent component

```javascript
  return (
    <form className="form-split-bill" onSubmit={handleSubmit}>
      <h2>Split a bill with {selectedFriend.name}</h2>

      <label>Bill value</label>
      <input
        type="text"
        value={bill}
        onChange={(e) => setBill(Number(e.target.value))}
      />

      <label>Your expense</label>
      <input
        type="text"
        value={paidByUser}
        onChange={(e) =>
          setPaidByUser(
            Number(e.target.value) > bill ? paidByUser : Number(e.target.value),
          )
        }
      />

      <label>{selectedFriend.name}'s expense</label>
      <input type="text" value={paidByFriend} disabled />

      <label>Who is paying the bill</label>
      <select
        value={whoIsPaying}
        onChange={(e) => setWhoIsPaying(e.target.value)}
      >
        <option value="me">Me</option>
        <option value="friend">{selectedFriend.name}</option>
      </select>

      <Button>Split Bill</Button>
    </form>
  );
}
```
- **Baris 189-225**: Render form split bill
- `<h2>`: Dynamic heading dengan nama teman
- Input 1 - Bill value: Total tagihan
  - `Number(e.target.value)`: Convert string ke number
- Input 2 - Your expense: Berapa yang user bayar
  - Validation: `Number(e.target.value) > bill ? paidByUser : Number(e.target.value)`
  - Jika melebihi total → jangan update (tetap paidByUser)
  - Jika kurang dari total → update dengan value baru
- Input 3 - Friend expense: Computed value (read-only)
  - `disabled`: User tidak bisa edit
  - Value: `bill - paidByUser` (teman bayar sisanya)
- Select - Who is paying:
  - Option 1: "Me" (user yang bayar)
  - Option 2: Friend name (teman yang bayar)

---

### 3. **src/index.css** - Styling

#### **Bagian 1: CSS Variables dan Reset**

```css
:root {
  --color-lightest: #fff4e6;
  --color-light: #ffe8cc;
  --color-medium: #ffa94d;
  --color-dark: #ff922b;
}
```
- **Baris 1-6**: Definisikan CSS color variables (theme colors)
- Warna orange untuk tema aplikasi
- Digunakan di seluruh stylesheet dengan `var(--color-*)`

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
```
- **Baris 8-12**: CSS reset untuk semua elemen
- `margin: 0, padding: 0`: Hapus default margin/padding
- `box-sizing: border-box`: Include padding dalam width/height

```css
html {
  font-size: 62.5%;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
}
```
- **Baris 14-18**: Styling HTML element
- `font-size: 62.5%`: Set base font size (16px * 62.5% = 10px)
  - Berguna untuk rem unit (1rem = 10px, lebih mudah dihitung)
- `font-family`: System font stack (fallback jika font tidak tersedia)

```css
body {
  height: 100vh;
  color: #495057;
  display: flex;
  align-items: center;
  justify-content: center;
}
```
- **Baris 20-26**: Styling body element
- `height: 100vh`: Full viewport height
- `color: #495057`: Warna text default (gray)
- `display: flex`: Flexbox layout
- `align-items: center, justify-content: center`: Center content both vertically & horizontally

#### **Bagian 2: Main Layout**

```css
.app {
  min-height: 66vh;
  display: grid;
  grid-template-columns: 34rem 44rem;
  column-gap: 4rem;
  align-items: start;
}
```
- **Baris 28-34**: Main app container
- `display: grid`: CSS Grid layout
- `grid-template-columns: 34rem 44rem`: 2 kolom (sidebar 34rem, main 44rem)
- `column-gap: 4rem`: Space antara kolom
- `align-items: start`: Align items ke top (bukan center)

#### **Bagian 3: Button Styling**

```css
.button {
  background-color: var(--color-medium);
  color: #343a40;
  padding: 0.8rem 1.2rem;
  border: none;
  border-radius: 7px;
  font-weight: bold;
  cursor: pointer;
  transition: 0.3s;
}

.button:hover {
  background-color: var(--color-dark);
}
```
- **Baris 36-49**: Button styling
- `background-color: var(--color-medium)`: Orange background
- `color: #343a40`: Dark gray text
- `padding: 0.8rem 1.2rem`: Vertical & horizontal padding
- `border: none`: Hapus border default
- `border-radius: 7px`: Rounded corners
- `cursor: pointer`: Pointer cursor saat hover
- `transition: 0.3s`: Smooth animation untuk hover effect
- Hover state: Ubah background ke darker orange

#### **Bagian 4: Sidebar Styling**

```css
.sidebar ul {
  list-style: none;
  display: flex;
  flex-direction: column;
  gap: 0.4rem;
  font-size: 1.4rem;
  margin-bottom: 2rem;
}
```
- **Baris 51-58**: Styling untuk list teman
- `list-style: none`: Hapus bullet points
- `display: flex, flex-direction: column`: Vertical list
- `gap: 0.4rem`: Space antara setiap teman item
- `font-size: 1.4rem`: Ukuran font (14px)

```css
.sidebar li {
  display: grid;
  grid-template-columns: 4.8rem 1fr auto;
  align-items: center;
  column-gap: 1.6rem;

  padding: 1.2rem;
  border-radius: 7px;
  transition: 0.5s;
}
```
- **Baris 60-69**: Styling untuk setiap teman item
- `display: grid`: Grid layout dalam setiap list item
- `grid-template-columns: 4.8rem 1fr auto`:
  - Column 1: 4.8rem (avatar width)
  - Column 2: 1fr (flexible, nama dan balance)
  - Column 3: auto (button width)
- `align-items: center`: Vertical center
- `column-gap: 1.6rem`: Space antar kolom
- `padding: 1.2rem`: Inner spacing
- `transition: 0.5s`: Smooth animation untuk hover

```css
.selected,
.sidebar li:hover {
  background-color: var(--color-lightest);
}
```
- **Baris 71-74**: Styling untuk selected/hover state
- Light orange background ketika dipilih atau di-hover

```css
.sidebar li img {
  border-radius: 50%;
  width: 100%;
  grid-row: span 2;
}

.sidebar li h3 {
  grid-row: 1;
  grid-column: 2;
}

.sidebar li p {
  grid-row: 2;
  grid-column: 2;
}

.sidebar li .button {
  grid-row: span 2;
  grid-column: 3;
}
```
- **Baris 76-95**: Grid positioning dalam list item
- Avatar: `border-radius: 50%` (circular), span 2 rows
- Nama (h3): Row 1, Column 2
- Balance (p): Row 2, Column 2
- Button: Span 2 rows, Column 3

```css
.sidebar > .button {
  float: right;
  margin-right: 1.2rem;
}
```
- **Baris 97-100**: Positioning untuk "Add Friend" button
- `float: right`: Float ke kanan
- `margin-right: 1.2rem`: Spacing dari kanan

#### **Bagian 5: Color Classes**

```css
.green {
  color: #66a80f;
}

.red {
  color: #e03131;
}
```
- **Baris 102-108**: Color untuk status balance
- `.green`: Friend owes you (positive balance)
- `.red`: You owe friend (negative balance)

#### **Bagian 6: Form Styling**

```css
form {
  font-size: 1.6rem;
  display: grid;
  align-items: center;
  gap: 1.2rem;

  background-color: var(--color-lightest);
  border-radius: 7px;
}

.form-add-friend {
  grid-template-columns: 1fr 1.5fr;
  margin-bottom: 1.6rem;
  padding: 1.2rem;
}
.form-split-bill {
  grid-template-columns: 1fr 12rem;
  padding: 3.2rem 4rem;
}
```
- **Baris 110-128**: Form styling
- Grid layout untuk label dan input
- `.form-add-friend`: 2 kolom (sidebar form, compact)
- `.form-split-bill`: 2 kolom (main form, larger)

```css
label {
  font-weight: 500;
}

label::first-letter {
  display: inline-block;
  margin-right: 4px;
  font-size: 1.8rem;
}

input,
select {
  font-family: inherit;
  color: inherit;
  font-size: 1.5rem;
  padding: 0.7rem;
  text-align: center;
  border: 1px solid var(--color-light);
  border-radius: 4px;
  transition: 0.3s;
}

input:focus,
select:focus {
  outline: none;
  border: 1px solid var(--color-dark);
}
```
- **Baris 130-156**: Input/Select styling
- Label styling (bold first letter)
- Input: Border, padding, transition
- Focus state: Dark orange border

```css
form .button {
  margin-top: 0.6rem;
  grid-column: 2;
}

form h2 {
  grid-column: 1 / -1;
  font-size: 2.2rem;
  text-transform: uppercase;
  letter-spacing: -0.5px;
  margin-bottom: 1.6rem;
}
```
- **Baris 158-169**: Button dalam form (column 2, aligned right)
- Heading dalam form (span full width)

---

## Cara Menggunakan

1. **Lihat Daftar Teman**
   - Aplikasi menampilkan daftar teman dengan status saldo mereka
   - Status ditampilkan dengan warna (merah = berhutang, hijau = piutang)

2. **Tambah Teman Baru**
   - Klik tombol "Add Friend"
   - Isi nama teman dan URL image
   - Klik "Add Friend" untuk menyimpan

3. **Membagi Tagihan**
   - Klik "Select" pada teman yang ingin membagi tagihan
   - Form split bill akan muncul di sisi kanan
   - Isi total tagihan, biaya user, dan pilih siapa yang bayar
   - Klik "Split Bill" untuk menyimpan transaksi

4. **Deselect Teman**
   - Klik "Close" pada teman yang dipilih untuk deselect
   - Form split bill akan hilang

---

## Technology Stack

- **React 19.2.5**: Library untuk UI component
- **React DOM 19.2.5**: Library untuk render React ke DOM
- **React Scripts 5.0.1**: Build tools dan development server
- **CSS**: Pure CSS untuk styling (tidak ada CSS library)

---

## Scripts

```bash
npm start   # Jalankan development server
npm build   # Build untuk production
npm test    # Jalankan test suite
npm eject   # Eject (tidak bisa di-revert)
```

---

## Fitur Future (Potential Improvements)

- 💾 Local storage untuk persist data
- 📊 Export history transaksi
- 🎨 Dark mode toggle
- 📱 Responsive design untuk mobile
- 🔍 Search/filter teman
- 📝 Edit teman atau transaksi
- 🗑️ Delete teman atau transaksi

---

**Created**: 2026 | **Project**: Eat n Split
