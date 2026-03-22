import React, { useState, useEffect } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";

export default function ShopWebsite() {
  const [isAdmin, setIsAdmin] = useState(false);
  const [password, setPassword] = useState("");

  const defaultData = {
    "Glass Beads": [],
    "Acrylic Beads": [],
    "Findings & Charms": [],
    "Tassels & Elastic": [],
    "Cotton Balls": [],
    "Pom Pom Balls": [],
    "Kadi": []
  };

  const [items, setItems] = useState(() => {
    const saved = localStorage.getItem("shopItems");
    return saved ? JSON.parse(saved) : defaultData;
  });

  useEffect(() => {
    localStorage.setItem("shopItems", JSON.stringify(items));
  }, [items]);

  const [newItem, setNewItem] = useState({
    name: "",
    description: "",
    category: "Glass Beads",
    image: ""
  });

  const handleLogin = () => {
    if (password === "admin123") {
      setIsAdmin(true);
    } else {
      alert("Wrong password");
    }
  };

  const handleImageUpload = (e) => {
    const file = e.target.files[0];
    if (file) {
      const reader = new FileReader();
      reader.onloadend = () => {
        setNewItem({ ...newItem, image: reader.result });
      };
      reader.readAsDataURL(file);
    }
  };

  const addItem = () => {
    if (!newItem.name) return;
    setItems({
      ...items,
      [newItem.category]: [...items[newItem.category], newItem]
    });
    setNewItem({ name: "", description: "", category: "Glass Beads", image: "" });
  };

  const deleteItem = (category, index) => {
    const updated = { ...items };
    updated[category].splice(index, 1);
    setItems(updated);
  };

  return (
    <div className="min-h-screen bg-gray-50 p-6">
      <header className="flex justify-between items-center mb-8">
        <h1 className="text-2xl font-bold">THE ARTISAN ~ Fashion N Beads</h1>
        <nav className="space-x-4">
          <a href="#products">Materials</a>
          <a href="#contact">Contact</a>
        </nav>
      </header>

      <section className="text-center mb-10">
        <motion.h2
          initial={{ opacity: 0, y: -20 }}
          animate={{ opacity: 1, y: 0 }}
          className="text-3xl font-semibold mb-4"
        >
          All Jewelry Making Materials in One Place
        </motion.h2>
        <p className="text-gray-600">Browse & manage your stock easily</p>
      </section>

      {/* Admin */}
      <section className="mb-10">
        {!isAdmin ? (
          <div className="flex gap-2">
            <input
              type="password"
              placeholder="Admin Password"
              value={password}
              onChange={(e) => setPassword(e.target.value)}
              className="border p-2 rounded"
            />
            <Button onClick={handleLogin}>Admin Login</Button>
          </div>
        ) : (
          <div className="bg-white p-4 rounded-xl shadow space-y-2">
            <h2 className="font-semibold">Add New Item</h2>
            <input
              placeholder="Item Name"
              value={newItem.name}
              onChange={(e) => setNewItem({ ...newItem, name: e.target.value })}
              className="border p-2 rounded w-full"
            />
            <input
              placeholder="Description"
              value={newItem.description}
              onChange={(e) => setNewItem({ ...newItem, description: e.target.value })}
              className="border p-2 rounded w-full"
            />
            <select
              value={newItem.category}
              onChange={(e) => setNewItem({ ...newItem, category: e.target.value })}
              className="border p-2 rounded w-full"
            >
              {Object.keys(items).map((cat) => (
                <option key={cat}>{cat}</option>
              ))}
            </select>
            <input type="file" onChange={handleImageUpload} />
            <Button onClick={addItem}>Add Item</Button>
          </div>
        )}
      </section>

      {/* Products */}
      <section id="products" className="space-y-10">
        {Object.keys(items).map((category) => (
          <div key={category}>
            <h2 className="text-xl font-semibold mb-4">{category}</h2>
            <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
              {items[category].length === 0 ? (
                <p>No items yet</p>
              ) : (
                items[category].map((item, index) => (
                  <Card key={index} className="rounded-2xl shadow-md">
                    <CardContent className="p-4">
                      {item.image && (
                        <img src={item.image} className="h-40 w-full object-cover rounded mb-3" />
                      )}
                      <h3 className="font-medium">{item.name}</h3>
                      <p className="text-sm text-gray-500">{item.description}</p>

                      <a
                        href={`https://wa.me/919093674676?text=I am interested in ${item.name}`}
                        target="_blank"
                        className="block mt-2"
                      >
                        <Button className="w-full">Order on WhatsApp</Button>
                      </a>

                      {isAdmin && (
                        <Button
                          onClick={() => deleteItem(category, index)}
                          className="mt-2 w-full"
                        >
                          Delete
                        </Button>
                      )}
                    </CardContent>
                  </Card>
                ))
              )}
            </div>
          </div>
        ))}
      </section>

      {/* Contact */}
      <section id="contact" className="mt-10">
        <h2 className="text-xl font-semibold mb-2">Contact</h2>
        <p>Email: artisangazole@gmail.com</p>
        <p>WhatsApp: 9093674676</p>
        <p>Location: Shikshak Pally, Gazole, Malda, West Bengal - 732124</p>
        <a href="https://g.page/r/CTk710La9AmpEBE/review" className="text-blue-500 underline">View on Google Maps</a>
      </section>

      <footer className="text-center text-gray-500 mt-10">
        © {new Date().getFullYear()} THE ARTISAN ~ Fashion N Beads
      </footer>
    </div>
  );
}
