// server.js
const express = require('express');
const app = express();
const PORT = 3000;

// Middleware
app.use(express.json());

// In-memory data store
let items = [
  { id: 1, name: 'Item 1', description: 'This is item 1' },
  { id: 2, name: 'Item 2', description: 'This is item 2' }
];

// Helper function to find item by ID
const findItemById = (id) => items.find(item => item.id === parseInt(id));

// 1. Root route
app.get('/', (req, res) => {
  res.send('Hello World!');
});

// 2. CRUD Routes

// GET all items
app.get('/items', (req, res) => {
  res.json(items);
});

// GET single item by ID
app.get('/items/:id', (req, res) => {
  const item = findItemById(req.params.id);
  if (!item) {
    return res.status(404).json({ error: 'Item not found' });
  }
  res.json(item);
});

// POST create new item
app.post('/items', (req, res) => {
  // Validation
  if (!req.body.name || !req.body.description) {
    return res.status(400).json({ error: 'Name and description are required' });
  }

  const newItem = {
    id: items.length + 1,
    name: req.body.name,
    description: req.body.description
  };

  items.push(newItem);
  res.status(201).json(newItem);
});

// PUT update item by ID
app.put('/items/:id', (req, res) => {
  const item = findItemById(req.params.id);
  if (!item) {
    return res.status(404).json({ error: 'Item not found' });
  }

  // Validation
  if (!req.body.name || !req.body.description) {
    return res.status(400).json({ error: 'Name and description are required' });
  }

  item.name = req.body.name;
  item.description = req.body.description;
  res.json(item);
});

// DELETE item by ID
app.delete('/items/:id', (req, res) => {
  const itemIndex = items.findIndex(item => item.id === parseInt(req.params.id));
  if (itemIndex === -1) {
    return res.status(404).json({ error: 'Item not found' });
  }

  items.splice(itemIndex, 1);
  res.status(204).end();
});

// 3. Error handling for invalid routes
app.use((req, res) => {
  res.status(404).json({ error: 'Route not found' });
});

// Start server
app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
