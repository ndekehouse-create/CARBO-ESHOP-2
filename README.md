{
  "name": "carbo-eshop-frontend",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.14.1",
    "axios": "^1.4.0"
  },
  "scripts": {
    "start": "vite",
    "build": "vite build",
    "preview": "vite preview"
  }
}import React from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import Cart from './components/Cart';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/cart" element={<Cart />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;import React from 'react';
import { Link } from 'react-router-dom';

export default function Home() {
  return (
    <div>
      <h1>CARBO ESHOP</h1>
      <p>Vos abonnements à tout moment, en toute liberté !</p>
      <ul>
        <li>Netflix Premium 4K ULTRAHD - 2 500 FCFA</li>
        <li>Prime Video Premium - 2 000 FCFA</li>
        <li>Spotify Premium - 1 500 FCFA</li>
        <li>Crunchyroll Premium - 2 500 FCFA</li>
      </ul>
      <Link to="/cart">Voir le panier</Link>
    </div>
  );
}import React, { useState } from 'react';
import axios from 'axios';

export default function Cart() {
  const [cart] = useState([
    { id: 1, title: 'Netflix Premium', price: 2500, qty: 1 },
    { id: 2, title: 'Spotify Premium', price: 1500, qty: 1 }
  ]);

  const total = cart.reduce((s, p) => s + p.price * p.qty, 0);

  async function checkout() {
    try {
      const res = await axios.post(`${process.env.REACT_APP_API_URL}/api/orders`, {
        items: cart,
        amount: total,
        currency: 'XAF'
      });
      alert('Commande créée, redirection vers paiement');
      // Ici tu peux ajouter redirection Stripe / PayPal
    } catch (err) {
      alert('Erreur : ' + err.message);
    }
  }

  return (
    <div>
      <h2>Panier</h2>
      <ul>
        {cart.map(item => (
          <li key={item.id}>{item.title} - {item.price} FCFA x {item.qty}</li>
        ))}
      </ul>
      <strong>Total : {total} FCFA</strong>
      <br />
      <button onClick={checkout}>Payer</button>
    </div>
  );
}{
  "name": "carbo-eshop-backend",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "cors": "^2.8.5",
    "body-parser": "^1.20.2",
    "dotenv": "^16.3.1"
  }
}const express = require('express');
const cors = require('cors');
const bodyParser = require('body-parser');
require('dotenv').config();

const app = express();
const PORT = process.env.PORT || 4242;

app.use(cors());
app.use(bodyParser.json());

app.post('/api/orders', (req, res) => {
  const { items, amount, currency } = req.body;
  console.log('Nouvelle commande', items, amount, currency);
  res.json({ orderId: Math.floor(Math.random() * 1000000) });
});

app.get('/', (req, res) => {
  res.send('CARBO ESHOP backend en ligne');
});

app.listen(PORT, () => console.log(`Backend en ligne sur http://localhost:${PORT}`));
