# nomtheory
online food ordering 


import React, { useState, useReducer, useMemo, useEffect, useRef } from "react";
import {
  Search,
  Plus,
  Minus,
  X,
  ChevronLeft,
  MapPin,
  Phone,
  User,
  Check,
  Clock,
  ShoppingBag,
  Bike,
  Wallet,
  Banknote,
  Smartphone,
} from "lucide-react";

/* ------------------------------------------------------------------ */
/*  Design tokens                                                      */
/* ------------------------------------------------------------------ */
const C = {
  ink: "#221A2E",
  inkSoft: "#352A46",
  paper: "#FAFAF8",
  paperDark: "#E9E6E2",
  card: "#FFFFFF",
  mustard: "#3AAED8",
  mustardDark: "#2C8FB3",
  chili: "#FF4B5C",
  chiliDark: "#E1394A",
  leaf: "#4C9A6A",
  charcoal: "#221A2E",
  fade: "#8A8590",
};

/* ------------------------------------------------------------------ */
/*  Menu data                                                           */
/* ------------------------------------------------------------------ */
const CATEGORIES = [
  "Trending",
  "Biryani & Mains",
  "Ramen & Sushi",
  "Pizza & Burgers",
  "South Indian & Chaat",
  "Sweets & Desserts",
  "Beverages",
];

const DISHES = [
  { id: 1, name: "Hyderabadi Chicken Biryani", restaurant: "Biryani House", category: "Biryani & Mains", price: 160, veg: false, spice: 2, trending: true, desc: "Slow-cooked basmati layered with spiced chicken and fried onions." },
  { id: 2, name: "Veg Dum Biryani", restaurant: "Biryani House", category: "Biryani & Mains", price: 130, veg: true, spice: 1, trending: true, desc: "Basmati rice dum-cooked with seasonal vegetables and whole spices." },
  { id: 3, name: "Butter Chicken", restaurant: "Punjab Grill Express", category: "Biryani & Mains", price: 170, veg: false, spice: 1, desc: "Tandoori chicken in a mellow tomato-butter gravy." },
  { id: 4, name: "Paneer Butter Masala", restaurant: "Punjab Grill Express", category: "Biryani & Mains", price: 150, veg: true, spice: 1, desc: "Cottage cheese cubes in a rich cashew-tomato gravy." },
  { id: 5, name: "Mutton Rogan Josh", restaurant: "Punjab Grill Express", category: "Biryani & Mains", price: 200, veg: false, spice: 2, desc: "Slow-braised mutton in a Kashmiri red chilli gravy." },
  { id: 6, name: "Miso Ramen", restaurant: "Ramen Rebellion", category: "Ramen & Sushi", price: 180, veg: true, spice: 1, trending: true, desc: "Rich miso broth, wavy noodles, corn, egg and scallion." },
  { id: 7, name: "Chicken Chilli Cheese Ramen", restaurant: "Ramen Rebellion", category: "Ramen & Sushi", price: 220, veg: false, spice: 3, trending: true, desc: "Shredded chicken, chilli oil and cheese in a bold broth." },
  { id: 8, name: "Avocado Cream Cheese Sushi", restaurant: "Nori & Co", category: "Ramen & Sushi", price: 200, veg: true, spice: 0, desc: "Rolled sushi with avocado, cream cheese and toasted sesame." },
  { id: 9, name: "Prawn Tempura Sushi", restaurant: "Nori & Co", category: "Ramen & Sushi", price: 220, veg: false, spice: 0, desc: "Crisp battered prawns rolled with sushi rice and nori." },
  { id: 10, name: "Margherita Pizza", restaurant: "Pizza Studio", category: "Pizza & Burgers", price: 160, veg: true, spice: 0, trending: true, desc: "Fresh mozzarella, tomato and basil on a thin, charred crust." },
  { id: 11, name: "Chicken Tikka Pizza", restaurant: "Pizza Studio", category: "Pizza & Burgers", price: 200, veg: false, spice: 1, desc: "Tandoori chicken tikka, onions and mozzarella on a spiced base." },
  { id: 12, name: "Classic Cheeseburger", restaurant: "Burger Point", category: "Pizza & Burgers", price: 120, veg: false, spice: 1, desc: "Grilled beef patty, cheddar and house sauce in a soft bun." },
  { id: 13, name: "Crispy Veg Burger", restaurant: "Burger Point", category: "Pizza & Burgers", price: 100, veg: true, spice: 1, desc: "Crunchy potato-corn patty with slaw and chipotle mayo." },
  { id: 14, name: "Masala Dosa", restaurant: "Udupi Corner", category: "South Indian & Chaat", price: 80, veg: true, spice: 1, trending: true, desc: "Crisp rice crepe filled with spiced potato masala, served with chutney and sambar." },
  { id: 15, name: "Idli Sambar (4 pc)", restaurant: "Udupi Corner", category: "South Indian & Chaat", price: 70, veg: true, spice: 0, desc: "Steamed rice cakes served with sambar and coconut chutney." },
  { id: 16, name: "Pani Puri (6 pc)", restaurant: "Chaat Galli", category: "South Indian & Chaat", price: 40, veg: true, spice: 2, trending: true, desc: "Crisp puris filled with spiced water, tamarind and potato." },
  { id: 17, name: "Sev Puri", restaurant: "Chaat Galli", category: "South Indian & Chaat", price: 60, veg: true, spice: 1, desc: "Crisp puris topped with potato, chutneys and sev." },
  { id: 18, name: "Gulab Jamun (2 pc)", restaurant: "Mithai Wala", category: "Sweets & Desserts", price: 50, veg: true, spice: 0, desc: "Milk-solid dumplings soaked in cardamom syrup." },
  { id: 19, name: "Rasmalai (2 pc)", restaurant: "Mithai Wala", category: "Sweets & Desserts", price: 70, veg: true, spice: 0, desc: "Soft cottage-cheese discs in saffron-cardamom milk." },
  { id: 20, name: "Choco Lava Cake", restaurant: "Sugar Rush", category: "Sweets & Desserts", price: 100, veg: true, spice: 0, trending: true, desc: "Warm chocolate cake with a molten centre, served with ice cream." },
  { id: 21, name: "Masala Chai", restaurant: "Chai Point", category: "Beverages", price: 20, veg: true, spice: 0, desc: "Strong-brewed tea simmered with ginger and spices." },
  { id: 22, name: "Cold Coffee", restaurant: "Chai Point", category: "Beverages", price: 80, veg: true, spice: 0, trending: true, desc: "Blended coffee, milk and a scoop of ice cream." },

  { id: 23, name: "Chicken 65 Biryani", restaurant: "Biryani House", category: "Biryani & Mains", price: 180, veg: false, spice: 3, desc: "Dum biryani layered with crispy, deep-fried chicken 65." },
  { id: 24, name: "Egg Biryani", restaurant: "Biryani House", category: "Biryani & Mains", price: 140, veg: false, spice: 1, desc: "Basmati rice dum-cooked with boiled eggs and whole spices." },
  { id: 25, name: "Paneer Tikka Masala", restaurant: "Punjab Grill Express", category: "Biryani & Mains", price: 160, veg: true, spice: 2, desc: "Smoky grilled paneer in a spiced onion-tomato gravy." },
  { id: 26, name: "Dal Makhani", restaurant: "Punjab Grill Express", category: "Biryani & Mains", price: 130, veg: true, spice: 1, desc: "Black lentils simmered overnight with butter and cream." },
  { id: 27, name: "Chicken Tikka Masala", restaurant: "Punjab Grill Express", category: "Biryani & Mains", price: 190, veg: false, spice: 2, desc: "Char-grilled chicken tikka finished in a creamy tomato gravy." },

  { id: 28, name: "Tonkotsu Ramen", restaurant: "Ramen Rebellion", category: "Ramen & Sushi", price: 210, veg: false, spice: 1, desc: "Rich pork-bone broth with chashu, egg and scallion." },
  { id: 29, name: "Vegetable Tempura Udon", restaurant: "Ramen Rebellion", category: "Ramen & Sushi", price: 190, veg: true, spice: 0, desc: "Thick udon noodles in a light broth with crisp vegetable tempura." },
  { id: 30, name: "Spicy Tuna Roll", restaurant: "Nori & Co", category: "Ramen & Sushi", price: 220, veg: false, spice: 2, desc: "Seared tuna rolled with spicy mayo and cucumber." },
  { id: 31, name: "Vegetable Maki Roll", restaurant: "Nori & Co", category: "Ramen & Sushi", price: 170, veg: true, spice: 0, desc: "Cucumber, carrot and avocado rolled in seasoned rice and nori." },
  { id: 32, name: "Chicken Katsu Ramen", restaurant: "Ramen Rebellion", category: "Ramen & Sushi", price: 220, veg: false, spice: 1, desc: "Crispy chicken katsu served over noodles in a savoury broth." },

  { id: 33, name: "BBQ Chicken Pizza", restaurant: "Pizza Studio", category: "Pizza & Burgers", price: 220, veg: false, spice: 1, desc: "Smoky barbecue chicken, onions and mozzarella on a tangy base." },
  { id: 34, name: "Farmhouse Veg Pizza", restaurant: "Pizza Studio", category: "Pizza & Burgers", price: 180, veg: true, spice: 0, desc: "Loaded with capsicum, onion, tomato, olives and mozzarella." },
  { id: 35, name: "Spicy Paneer Burger", restaurant: "Burger Point", category: "Pizza & Burgers", price: 110, veg: true, spice: 2, desc: "Crumb-fried paneer patty with a fiery mayo and pickled onions." },
  { id: 36, name: "Grilled Chicken Burger", restaurant: "Burger Point", category: "Pizza & Burgers", price: 140, veg: false, spice: 1, desc: "Grilled chicken breast, lettuce and honey-mustard sauce." },
  { id: 37, name: "Mushroom Melt Burger", restaurant: "Burger Point", category: "Pizza & Burgers", price: 110, veg: true, spice: 0, desc: "Sauteed mushrooms and melted cheese in a soft bun." },

  { id: 38, name: "Rava Dosa", restaurant: "Udupi Corner", category: "South Indian & Chaat", price: 90, veg: true, spice: 1, desc: "Crisp, lacy semolina crepe served with chutney and sambar." },
  { id: 39, name: "Chettinad Chicken", restaurant: "Udupi Corner", category: "South Indian & Chaat", price: 170, veg: false, spice: 3, desc: "Chicken simmered in a fiery South Indian spice blend." },
  { id: 40, name: "Medu Vada (2 pc)", restaurant: "Udupi Corner", category: "South Indian & Chaat", price: 60, veg: true, spice: 1, desc: "Crisp, fluffy lentil doughnuts served with sambar and chutney." },
  { id: 41, name: "Bhel Puri", restaurant: "Chaat Galli", category: "South Indian & Chaat", price: 50, veg: true, spice: 1, desc: "Puffed rice tossed with sev, onion, tomato and tangy chutneys." },
  { id: 42, name: "Egg Bhurji Pav", restaurant: "Chaat Galli", category: "South Indian & Chaat", price: 90, veg: false, spice: 2, desc: "Spiced scrambled eggs served with buttered pav." },

  { id: 43, name: "Caramel Custard", restaurant: "Sugar Rush", category: "Sweets & Desserts", price: 90, veg: false, spice: 0, desc: "Silky egg custard set over a layer of soft caramel." },
  { id: 44, name: "Kulfi (2 stick)", restaurant: "Sugar Rush", category: "Sweets & Desserts", price: 80, veg: true, spice: 0, desc: "Dense, slow-churned milk ice cream with pistachio and cardamom." },
  { id: 45, name: "Kesar Pista Ice Cream", restaurant: "Sugar Rush", category: "Sweets & Desserts", price: 90, veg: true, spice: 0, desc: "Saffron and pistachio ice cream, churned the traditional way." },
  { id: 46, name: "Motichoor Ladoo (2 pc)", restaurant: "Mithai Wala", category: "Sweets & Desserts", price: 60, veg: true, spice: 0, desc: "Tiny fried gram-flour pearls bound in sugar syrup." },
  { id: 47, name: "Jalebi", restaurant: "Mithai Wala", category: "Sweets & Desserts", price: 60, veg: true, spice: 0, desc: "Crisp, syrup-soaked spirals, served warm." },

  { id: 48, name: "Virgin Mojito", restaurant: "Chai Point", category: "Beverages", price: 70, veg: true, spice: 0, desc: "Lime, mint and soda over crushed ice." },
  { id: 49, name: "Fresh Lime Soda", restaurant: "Chai Point", category: "Beverages", price: 40, veg: true, spice: 0, desc: "Sweet or salted fresh lime with chilled soda." },
  { id: 50, name: "Mango Lassi", restaurant: "Chai Point", category: "Beverages", price: 70, veg: true, spice: 0, desc: "Thick yoghurt smoothie blended with ripe mango." },
  { id: 51, name: "Egg White Protein Shake", restaurant: "Chai Point", category: "Beverages", price: 90, veg: false, spice: 0, desc: "Egg whites blended with milk, banana and a touch of honey." },
  { id: 52, name: "Filter Coffee", restaurant: "Chai Point", category: "Beverages", price: 40, veg: true, spice: 0, desc: "South Indian filter coffee, brewed strong with chicory." },
];

const DISHES_BY_ID = Object.fromEntries(DISHES.map((d) => [d.id, d]));

/* ------------------------------------------------------------------ */
/*  Cart reducer                                                        */
/* ------------------------------------------------------------------ */
function cartReducer(state, action) {
  switch (action.type) {
    case "ADD": {
      const existing = state.find((i) => i.id === action.id);
      if (existing) {
        return state.map((i) => (i.id === action.id ? { ...i, qty: i.qty + 1 } : i));
      }
      return [...state, { id: action.id, qty: 1 }];
    }
    case "INCREMENT":
      return state.map((i) => (i.id === action.id ? { ...i, qty: i.qty + 1 } : i));
    case "DECREMENT":
      return state
        .map((i) => (i.id === action.id ? { ...i, qty: i.qty - 1 } : i))
        .filter((i) => i.qty > 0);
    case "REMOVE":
      return state.filter((i) => i.id !== action.id);
    case "CLEAR":
      return [];
    default:
      return state;
  }
}

const rupee = (n) => `\u20B9${n.toFixed(0)}`;

/* ------------------------------------------------------------------ */
/*  Small presentational pieces                                        */
/* ------------------------------------------------------------------ */
function VegDot({ veg }) {
  return (
    <span
      style={{
        display: "inline-flex",
        alignItems: "center",
        justifyContent: "center",
        width: 14,
        height: 14,
        border: `1.5px solid ${veg ? C.leaf : C.chili}`,
        borderRadius: 3,
        flexShrink: 0,
      }}
      aria-label={veg ? "Vegetarian" : "Non-vegetarian"}
      title={veg ? "Vegetarian" : "Non-vegetarian"}
    >
      <span
        style={{
          width: 7,
          height: 7,
          borderRadius: veg ? "50%" : "50%",
          background: veg ? C.leaf : C.chili,
        }}
      />
    </span>
  );
}

function SpiceMarks({ level }) {
  if (!level) return null;
  return (
    <span style={{ color: C.chili, fontSize: 11, letterSpacing: 1 }}>
      {"\u25CF".repeat(level)}
      <span style={{ color: "#D8CDB4" }}>{"\u25CF".repeat(3 - level)}</span>
    </span>
  );
}

function CategoryChip({ label, active, onClick, dark }) {
  const activeBg = dark ? C.mustard : C.ink;
  const activeText = dark ? C.ink : C.paper;
  const idleText = dark ? C.paper : C.charcoal;
  const idleBorder = dark ? C.inkSoft : C.paperDark;
  return (
    <button
      onClick={onClick}
      className="flex-shrink-0 whitespace-nowrap rounded-full px-4 py-2 text-sm font-medium transition-colors"
      style={{
        background: active ? activeBg : "transparent",
        color: active ? activeText : idleText,
        border: `1.5px solid ${active ? activeBg : idleBorder}`,
      }}
    >
      {label}
    </button>
  );
}

/* ------------------------------------------------------------------ */
/*  Header                                                              */
/* ------------------------------------------------------------------ */
function Header({
  orderType,
  setOrderType,
  cartCount,
  onCartClick,
  showCategories,
  categories,
  activeCategory,
  onSelectCategory,
}) {
  return (
    <header style={{ background: C.ink }} className="sticky top-0 z-30">
      <div className="mx-auto max-w-5xl px-4 py-4 flex items-center justify-between gap-3">
        <div className="flex items-center gap-2">
          <div>
            <h1
              style={{ fontFamily: "'Space Grotesk', sans-serif", color: C.chili, letterSpacing: "-0.02em" }}
              className="text-2xl font-bold leading-none"
            >
              nomtheory
            </h1>
          </div>
        </div>

        <div className="flex items-center gap-2">
          <div
            style={{ background: C.inkSoft, borderRadius: 999 }}
            className="hidden sm:flex p-1"
          >
            {["Delivery", "Pickup"].map((t) => (
              <button
                key={t}
                onClick={() => setOrderType(t)}
                style={{
                  background: orderType === t ? C.mustard : "transparent",
                  color: orderType === t ? C.ink : C.paper,
                }}
                className="flex items-center gap-1.5 rounded-full px-3 py-1.5 text-sm font-medium transition-colors"
              >
                {t === "Delivery" ? <Bike size={15} /> : <ShoppingBag size={15} />}
                {t}
              </button>
            ))}
          </div>

          <button
            onClick={onCartClick}
            style={{ background: C.mustard, color: C.ink }}
            className="relative flex items-center gap-1.5 rounded-full px-3.5 py-2 text-sm font-semibold"
          >
            <ShoppingBag size={17} />
            <span className="hidden sm:inline">Cart</span>
            {cartCount > 0 && (
              <span
                style={{ background: C.chili, color: "#fff" }}
                className="absolute -top-1.5 -right-1.5 flex h-5 min-w-5 items-center justify-center rounded-full px-1 text-[11px] font-bold"
              >
                {cartCount}
              </span>
            )}
          </button>
        </div>
      </div>

      {showCategories && (
        <div style={{ borderTop: `1px solid ${C.inkSoft}` }}>
          <div className="mx-auto max-w-5xl px-4">
            <div className="flex gap-2 overflow-x-auto py-3 no-scrollbar">
              {categories.map((cat) => (
                <CategoryChip
                  key={cat}
                  label={cat}
                  active={activeCategory === cat}
                  onClick={() => onSelectCategory(cat)}
                  dark
                />
              ))}
            </div>
          </div>
        </div>
      )}
    </header>
  );
}

/* ------------------------------------------------------------------ */
/*  Dish card                                                           */
/* ------------------------------------------------------------------ */
function DishCard({ dish, quantity, onAdd, onIncrement, onDecrement }) {
  return (
    <div
      style={{ background: C.card, border: `1px solid ${C.paperDark}` }}
      className="flex flex-col justify-between rounded-2xl p-4"
    >
      <div>
        <div className="flex items-start justify-between gap-2">
          <div className="flex items-start gap-2">
            <VegDot veg={dish.veg} />
            <div>
              <h3 style={{ color: C.charcoal }} className="font-semibold text-[15px] leading-snug">
                {dish.name}
              </h3>
              <p style={{ color: C.mustardDark }} className="text-xs font-medium mt-0.5">
                {dish.restaurant}
              </p>
            </div>
          </div>
          <SpiceMarks level={dish.spice} />
        </div>
        <p style={{ color: C.fade }} className="mt-1.5 text-sm leading-snug">
          {dish.desc}
        </p>
      </div>

      <div className="mt-4 flex items-center justify-between">
        <span style={{ color: C.charcoal, fontFamily: "'Space Grotesk', sans-serif" }} className="text-lg">
          {rupee(dish.price)}
        </span>

        {quantity > 0 ? (
          <div
            style={{ background: C.ink }}
            className="flex items-center gap-3 rounded-full px-2.5 py-1.5"
          >
            <button onClick={onDecrement} aria-label={`Remove one ${dish.name}`}>
              <Minus size={15} color={C.paper} />
            </button>
            <span style={{ color: C.paper }} className="w-4 text-center text-sm font-semibold">
              {quantity}
            </span>
            <button onClick={onIncrement} aria-label={`Add one more ${dish.name}`}>
              <Plus size={15} color={C.paper} />
            </button>
          </div>
        ) : (
          <button
            onClick={onAdd}
            style={{ background: C.mustard, color: C.ink }}
            className="rounded-full px-4 py-1.5 text-sm font-semibold"
          >
            Add
          </button>
        )}
      </div>
    </div>
  );
}

/* ------------------------------------------------------------------ */
/*  Cart drawer                                                         */
/* ------------------------------------------------------------------ */
function CartDrawer({
  isOpen,
  onClose,
  items,
  onIncrement,
  onDecrement,
  onRemove,
  subtotal,
  deliveryFee,
  tax,
  total,
  orderType,
  onCheckout,
}) {
  return (
    <>
      <div
        onClick={onClose}
        style={{
          background: "rgba(27,35,64,0.45)",
          opacity: isOpen ? 1 : 0,
          pointerEvents: isOpen ? "auto" : "none",
          transition: "opacity 0.25s ease",
        }}
        className="fixed inset-0 z-40"
      />
      <aside
        style={{
          background: C.paper,
          transform: isOpen ? "translateX(0)" : "translateX(100%)",
          transition: "transform 0.3s ease",
          width: "min(400px, 100vw)",
        }}
        className="fixed right-0 top-0 z-50 flex h-full flex-col"
      >
        <div
          style={{ background: C.ink, color: C.paper }}
          className="flex items-center justify-between px-5 py-4"
        >
          <h2 style={{ fontFamily: "'Space Grotesk', sans-serif" }} className="text-lg">
            Your cart
          </h2>
          <button onClick={onClose} aria-label="Close cart">
            <X size={20} />
          </button>
        </div>

        {items.length === 0 ? (
          <div className="flex flex-1 flex-col items-center justify-center gap-2 px-6 text-center">
            <ShoppingBag size={34} color={C.fade} />
            <p style={{ color: C.fade }} className="text-sm">
              Nothing here yet. Add a dish or two from the menu.
            </p>
          </div>
        ) : (
          <>
            <div className="flex-1 overflow-y-auto px-5 py-4 space-y-3">
              {items.map(({ id, qty }) => {
                const dish = DISHES_BY_ID[id];
                return (
                  <div
                    key={id}
                    style={{ background: C.card, border: `1px solid ${C.paperDark}` }}
                    className="flex items-center justify-between gap-2 rounded-xl p-3"
                  >
                    <div className="flex items-start gap-2 min-w-0">
                      <VegDot veg={dish.veg} />
                      <div className="min-w-0">
                        <p style={{ color: C.charcoal }} className="truncate text-sm font-medium">
                          {dish.name}
                        </p>
                        <p style={{ color: C.fade }} className="text-xs">
                          {rupee(dish.price)} each
                        </p>
                      </div>
                    </div>
                    <div className="flex items-center gap-2 shrink-0">
                      <div
                        style={{ background: C.paperDark }}
                        className="flex items-center gap-2 rounded-full px-2 py-1"
                      >
                        <button onClick={() => onDecrement(id)} aria-label="Decrease quantity">
                          <Minus size={13} color={C.charcoal} />
                        </button>
                        <span className="w-3 text-center text-xs font-semibold" style={{ color: C.charcoal }}>
                          {qty}
                        </span>
                        <button onClick={() => onIncrement(id)} aria-label="Increase quantity">
                          <Plus size={13} color={C.charcoal} />
                        </button>
                      </div>
                      <button onClick={() => onRemove(id)} aria-label={`Remove ${dish.name}`}>
                        <X size={15} color={C.fade} />
                      </button>
                    </div>
                  </div>
                );
              })}
            </div>

            <div style={{ borderTop: `1px solid ${C.paperDark}` }} className="px-5 py-4 space-y-1.5">
              <Row label="Subtotal" value={rupee(subtotal)} />
              <Row
                label={orderType === "Delivery" ? "Delivery fee" : "Service"}
                value={deliveryFee === 0 ? "Free" : rupee(deliveryFee)}
              />
              <Row label="GST (5%)" value={rupee(tax)} />
              <div
                style={{ borderTop: `1px dashed ${C.fade}` }}
                className="mt-2 flex items-center justify-between pt-2"
              >
                <span style={{ color: C.charcoal }} className="font-semibold">
                  Total
                </span>
                <span
                  style={{ color: C.charcoal, fontFamily: "'Space Grotesk', sans-serif" }}
                  className="text-lg"
                >
                  {rupee(total)}
                </span>
              </div>
              <button
                onClick={onCheckout}
                style={{ background: C.chili, color: "#fff" }}
                className="mt-3 w-full rounded-full py-3 text-sm font-semibold"
              >
                Proceed to checkout
              </button>
            </div>
          </>
        )}
      </aside>
    </>
  );
}

function Row({ label, value }) {
  return (
    <div className="flex items-center justify-between text-sm">
      <span style={{ color: C.fade }}>{label}</span>
      <span style={{ color: C.charcoal }}>{value}</span>
    </div>
  );
}

/* ------------------------------------------------------------------ */
/*  Confirmation view                                                    */
/* ------------------------------------------------------------------ */
/* ------------------------------------------------------------------ */
/*  Checkout view                                                       */
/* ------------------------------------------------------------------ */
function CheckoutView({
  form,
  setForm,
  items,
  subtotal,
  deliveryFee,
  tax,
  total,
  orderType,
  onBack,
  onPlaceOrder,
}) {
  const [errors, setErrors] = useState({});

  function handleSubmit(e) {
    e.preventDefault();
    const next = {};
    if (!form.name.trim()) next.name = "Enter your name";
    if (!/^\d{10}$/.test(form.phone.trim())) next.phone = "Enter a 10-digit phone number";
    if (orderType === "Delivery" && !form.address.trim()) next.address = "Enter a delivery address";
    setErrors(next);
    if (Object.keys(next).length === 0) onPlaceOrder();
  }

  return (
    <div className="mx-auto max-w-2xl px-4 py-8">
      <button
        onClick={onBack}
        style={{ color: C.charcoal }}
        className="mb-5 flex items-center gap-1 text-sm font-medium"
      >
        <ChevronLeft size={16} /> Back to menu
      </button>

      <h2 style={{ fontFamily: "'Space Grotesk', sans-serif", color: C.charcoal }} className="text-2xl mb-1">
        Checkout
      </h2>
      <p style={{ color: C.fade }} className="text-sm mb-6">
        {orderType === "Delivery"
          ? "We'll dispatch this the moment it's confirmed."
          : "Show your order number at the counter when you arrive."}
      </p>

      <form onSubmit={handleSubmit} className="space-y-4">
        <Field
          label="Full name"
          icon={<User size={16} color={C.fade} />}
          value={form.name}
          error={errors.name}
          onChange={(v) => setForm({ ...form, name: v })}
          placeholder="Asha Kulkarni"
        />
        <Field
          label="Phone number"
          icon={<Phone size={16} color={C.fade} />}
          value={form.phone}
          error={errors.phone}
          onChange={(v) => setForm({ ...form, phone: v.replace(/[^\d]/g, "").slice(0, 10) })}
          placeholder="9876543210"
        />
        {orderType === "Delivery" && (
          <Field
            label="Delivery address"
            icon={<MapPin size={16} color={C.fade} />}
            value={form.address}
            error={errors.address}
            onChange={(v) => setForm({ ...form, address: v })}
            placeholder="Flat, building, street, area"
            textarea
          />
        )}

        <div>
          <label style={{ color: C.charcoal }} className="mb-2 block text-sm font-medium">
            Payment method
          </label>
          <div className="grid grid-cols-3 gap-2">
            {[
              { id: "UPI", label: "UPI", icon: <Smartphone size={16} /> },
              { id: "Card", label: "Card", icon: <Wallet size={16} /> },
              { id: "Cash", label: "Cash", icon: <Banknote size={16} /> },
            ].map((p) => (
              <button
                type="button"
                key={p.id}
                onClick={() => setForm({ ...form, payment: p.id })}
                style={{
                  background: form.payment === p.id ? C.ink : C.card,
                  color: form.payment === p.id ? C.paper : C.charcoal,
                  border: `1px solid ${form.payment === p.id ? C.ink : C.paperDark}`,
                }}
                className="flex flex-col items-center justify-center gap-1 rounded-xl py-3 text-xs font-medium"
              >
                {p.icon}
                {p.label}
              </button>
            ))}
          </div>
        </div>

        <div
          style={{ background: C.card, border: `1px solid ${C.paperDark}` }}
          className="rounded-xl p-4 space-y-1.5"
        >
          <p style={{ color: C.charcoal }} className="mb-2 text-sm font-semibold">
            {items.length} item{items.length !== 1 ? "s" : ""}
          </p>
          <Row label="Subtotal" value={rupee(subtotal)} />
          <Row
            label={orderType === "Delivery" ? "Delivery fee" : "Service"}
            value={deliveryFee === 0 ? "Free" : rupee(deliveryFee)}
          />
          <Row label="GST (5%)" value={rupee(tax)} />
          <div
            style={{ borderTop: `1px dashed ${C.fade}` }}
            className="mt-2 flex items-center justify-between pt-2"
          >
            <span style={{ color: C.charcoal }} className="font-semibold">
              Total
            </span>
            <span style={{ color: C.charcoal, fontFamily: "'Space Grotesk', sans-serif" }} className="text-lg">
              {rupee(total)}
            </span>
          </div>
        </div>

        <button
          type="submit"
          style={{ background: C.chili, color: "#fff" }}
          className="w-full rounded-full py-3.5 text-sm font-semibold"
        >
          Place order &middot; {rupee(total)}
        </button>
      </form>
    </div>
  );
}

function Field({ label, icon, value, onChange, error, placeholder, textarea }) {
  const Tag = textarea ? "textarea" : "input";
  return (
    <div>
      <label style={{ color: C.charcoal }} className="mb-1.5 block text-sm font-medium">
        {label}
      </label>
      <div
        style={{
          background: C.card,
          border: `1px solid ${error ? C.chili : C.paperDark}`,
        }}
        className="flex items-start gap-2 rounded-xl px-3 py-2.5"
      >
        <span className="mt-0.5">{icon}</span>
        <Tag
          value={value}
          onChange={(e) => onChange(e.target.value)}
          placeholder={placeholder}
          rows={textarea ? 2 : undefined}
          style={{ color: C.charcoal }}
          className="w-full resize-none bg-transparent text-sm outline-none"
        />
      </div>
      {error && (
        <p style={{ color: C.chili }} className="mt-1 text-xs">
          {error}
        </p>
      )}
    </div>
  );
}

/* ------------------------------------------------------------------ */
/*  Confirmation view                                                    */
/* ------------------------------------------------------------------ */
function ConfirmationView({ order, onReset }) {
  return (
    <div className="mx-auto flex max-w-md flex-col items-center px-4 py-16 text-center">
      <div
        style={{ background: C.leaf }}
        className="mb-5 flex h-16 w-16 items-center justify-center rounded-full"
      >
        <Check size={30} color="#fff" />
      </div>
      <h2 style={{ fontFamily: "'Space Grotesk', sans-serif", color: C.charcoal }} className="text-2xl">
        Order confirmed
      </h2>
      <p style={{ color: C.fade }} className="mt-2 text-sm">
        Your order is confirmed and heading to the kitchen. We've sent the details to {order.phone}.
      </p>

      <div
        style={{ background: C.card, border: `1px solid ${C.paperDark}` }}
        className="mt-6 w-full rounded-2xl p-5 text-left"
      >
        <Row label="Order number" value={order.id} />
        <Row label="Name" value={order.name} />
        {order.orderType === "Delivery" && <Row label="Address" value={order.address} />}
        <Row label="Payment" value={order.payment} />
        <div
          style={{ borderTop: `1px dashed ${C.fade}` }}
          className="mt-2 flex items-center justify-between pt-2"
        >
          <span style={{ color: C.charcoal }} className="font-semibold">
            Total paid
          </span>
          <span style={{ color: C.charcoal, fontFamily: "'Space Grotesk', sans-serif" }} className="text-lg">
            {rupee(order.total)}
          </span>
        </div>
        <div className="mt-3 flex items-center gap-2" style={{ color: C.mustardDark }}>
          <Clock size={16} />
          <span className="text-sm font-medium">
            {order.orderType === "Delivery" ? "Arriving in 35\u201345 min" : "Ready in 20\u201325 min"}
          </span>
        </div>
      </div>

      <button
        onClick={onReset}
        style={{ background: C.ink, color: C.paper }}
        className="mt-8 rounded-full px-6 py-3 text-sm font-semibold"
      >
        Order more food
      </button>
    </div>
  );
}

/* ------------------------------------------------------------------ */
/*  Toast                                                                */
/* ------------------------------------------------------------------ */
function Toast({ message }) {
  if (!message) return null;
  return (
    <div
      style={{ background: C.ink, color: C.paper }}
      className="fixed bottom-5 left-1/2 z-50 -translate-x-1/2 rounded-full px-4 py-2.5 text-sm font-medium shadow-lg"
    >
      {message}
    </div>
  );
}

/* ------------------------------------------------------------------ */
/*  App                                                                  */
/* ------------------------------------------------------------------ */
export default function App() {
  const [cart, dispatch] = useReducer(cartReducer, []);
  const [view, setView] = useState("menu"); // menu | checkout | confirmation
  const [isCartOpen, setIsCartOpen] = useState(false);
  const [search, setSearch] = useState("");
  const [activeCategory, setActiveCategory] = useState("Trending");
  const [orderType, setOrderType] = useState("Delivery");
  const [form, setForm] = useState({ name: "", phone: "", address: "", payment: "UPI" });
  const [lastOrder, setLastOrder] = useState(null);
  const [toast, setToast] = useState(null);
  const toastTimer = useRef(null);

  const filteredDishes = useMemo(() => {
    return DISHES.filter((d) => {
      let matchesCategory;
      if (activeCategory === "Trending") matchesCategory = d.trending;
      else matchesCategory = d.category === activeCategory;

      const matchesSearch =
        d.name.toLowerCase().includes(search.toLowerCase()) ||
        d.restaurant.toLowerCase().includes(search.toLowerCase());
      return matchesCategory && matchesSearch;
    }).sort((a, b) => Number(b.veg) - Number(a.veg));
  }, [search, activeCategory]);

  const cartCount = cart.reduce((sum, i) => sum + i.qty, 0);
  const subtotal = cart.reduce((sum, i) => sum + DISHES_BY_ID[i.id].price * i.qty, 0);
  const deliveryFee = orderType === "Delivery" ? (subtotal === 0 || subtotal >= 200 ? 0 : 30) : 0;
  const tax = Math.round(subtotal * 0.05);
  const total = subtotal + deliveryFee + tax;

  function showToast(message) {
    setToast(message);
    clearTimeout(toastTimer.current);
    toastTimer.current = setTimeout(() => setToast(null), 1800);
  }

  function handleAdd(dish) {
    dispatch({ type: "ADD", id: dish.id });
    showToast(`${dish.name} added to your cart`);
  }

  function handlePlaceOrder() {
    const id = "NT" + Math.floor(10000 + Math.random() * 89999);
    setLastOrder({
      id,
      name: form.name,
      phone: form.phone,
      address: form.address,
      payment: form.payment,
      orderType,
      total,
    });
    dispatch({ type: "CLEAR" });
    setForm({ name: "", phone: "", address: "", payment: "UPI" });
    setIsCartOpen(false);
    setView("confirmation");
  }

  function handleReset() {
    setLastOrder(null);
    setView("menu");
  }

  useEffect(() => {
    if (cart.length === 0 && view === "checkout") {
      setView("menu");
    }
  }, [cart.length, view]);

  return (
    <div style={{ background: C.paper, minHeight: "100vh", fontFamily: "'Inter', sans-serif" }}>
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@500;600;700&family=Inter:wght@400;500;600;700&display=swap');
        * { box-sizing: border-box; }
        input::placeholder, textarea::placeholder { color: ${C.fade}; opacity: 0.7; }
        ::-webkit-scrollbar { height: 6px; width: 6px; }
        ::-webkit-scrollbar-thumb { background: ${C.paperDark}; border-radius: 4px; }
        .no-scrollbar::-webkit-scrollbar { display: none; }
        .no-scrollbar { scrollbar-width: none; -ms-overflow-style: none; }
      `}</style>

      <Header
        orderType={orderType}
        setOrderType={setOrderType}
        cartCount={cartCount}
        onCartClick={() => setIsCartOpen(true)}
        showCategories={view === "menu"}
        categories={CATEGORIES}
        activeCategory={activeCategory}
        onSelectCategory={setActiveCategory}
      />

      {view === "menu" && (
        <main className="mx-auto max-w-5xl px-4 pb-24 pt-5">
            <div
              style={{ background: C.card, border: `1px solid ${C.paperDark}` }}
              className="flex items-center gap-2 rounded-full px-4 py-2.5 mb-6"
            >
              <Search size={17} color={C.fade} />
              <input
                value={search}
                onChange={(e) => setSearch(e.target.value)}
                placeholder="Search for a dish, e.g. biryani"
                style={{ color: C.charcoal }}
                className="w-full bg-transparent text-sm outline-none"
              />
            </div>

          {filteredDishes.length === 0 ? (
            <div className="py-16 text-center">
              <p style={{ color: C.fade }}>No dishes match "{search}". Try another search.</p>
            </div>
          ) : (
            <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
              {filteredDishes.map((dish) => {
                const inCart = cart.find((i) => i.id === dish.id);
                return (
                  <DishCard
                    key={dish.id}
                    dish={dish}
                    quantity={inCart ? inCart.qty : 0}
                    onAdd={() => handleAdd(dish)}
                    onIncrement={() => dispatch({ type: "INCREMENT", id: dish.id })}
                    onDecrement={() => dispatch({ type: "DECREMENT", id: dish.id })}
                  />
                );
              })}
            </div>
          )}
          </main>
      )}

      {view === "checkout" && (
        <CheckoutView
          form={form}
          setForm={setForm}
          items={cart}
          subtotal={subtotal}
          deliveryFee={deliveryFee}
          tax={tax}
          total={total}
          orderType={orderType}
          onBack={() => setView("menu")}
          onPlaceOrder={handlePlaceOrder}
        />
      )}

      {view === "confirmation" && lastOrder && (
        <ConfirmationView order={lastOrder} onReset={handleReset} />
      )}

      <CartDrawer
        isOpen={isCartOpen}
        onClose={() => setIsCartOpen(false)}
        items={cart}
        onIncrement={(id) => dispatch({ type: "INCREMENT", id })}
        onDecrement={(id) => dispatch({ type: "DECREMENT", id })}
        onRemove={(id) => dispatch({ type: "REMOVE", id })}
        subtotal={subtotal}
        deliveryFee={deliveryFee}
        tax={tax}
        total={total}
        orderType={orderType}
        onCheckout={() => {
          setIsCartOpen(false);
          setView("checkout");
        }}
      />

      <Toast message={toast} />
    </div>
  );
}
