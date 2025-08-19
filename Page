import React, { useMemo, useState } from "react";
import { Search, SlidersHorizontal, X, Check, Sparkles, ShoppingCart, FlaskConical } from "lucide-react";
import { motion, AnimatePresence } from "framer-motion";
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Input } from "@/components/ui/input";
import { Badge } from "@/components/ui/badge";
import { Sheet, SheetContent, SheetHeader, SheetTitle, SheetTrigger } from "@/components/ui/sheet";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { DropdownMenu, DropdownMenuContent, DropdownMenuItem, DropdownMenuLabel, DropdownMenuSeparator, DropdownMenuTrigger } from "@/components/ui/dropdown-menu";

// ---------------------------------------------
// Demo data (replace with API later)
// ---------------------------------------------
const PRODUCTS = [
  {
    id: "p1",
    brand: "GlowNova",
    name: "Radiant Skin Serum",
    category: "Serum",
    price: 1499,
    rating: 4.6,
    image: "https://images.unsplash.com/photo-1608571425502-3a3b0d79c2d6?q=80&w=800&auto=format&fit=crop",
    shades: [],
    ingredients: [
      "Aqua",
      "Niacinamide",
      "Glycerin",
      "Butylene Glycol",
      "Panthenol",
      "Sodium Hyaluronate",
      "Allantoin",
      "Phenoxyethanol",
    ],
    tags: ["oil-free", "fragrance-free", "daily"],
  },
  {
    id: "p2",
    brand: "LuxéLab",
    name: "Velvet Matte Lipstick",
    category: "Lipstick",
    price: 999,
    rating: 4.4,
    image: "https://images.unsplash.com/photo-1611930022073-b7a4ba5fcccd?q=80&w=800&auto=format&fit=crop",
    shades: ["Ruby 01", "Mocha 07", "Rosy 13"],
    ingredients: [
      "Ricinus Communis (Castor) Seed Oil",
      "Candelilla Wax",
      "Isododecane",
      "Silica",
      "Tocopherol",
      "Red 7 Lake",
      "Fragrance",
    ],
    tags: ["long-wear", "matte"],
  },
  {
    id: "p3",
    brand: "PureForm",
    name: "HydraFix Gel Moisturizer",
    category: "Moisturizer",
    price: 1299,
    rating: 4.5,
    image: "https://images.unsplash.com/photo-1612815154858-60aa4c59eaa0?q=80&w=800&auto=format&fit=crop",
    shades: [],
    ingredients: [
      "Aqua",
      "Glycerin",
      "Dimethicone",
      "Carbomer",
      "Sodium Hyaluronate",
      "Aloe Barbadensis Leaf Juice",
      "Phenoxyethanol",
    ],
    tags: ["gel", "lightweight", "non-comedogenic"],
  },
  {
    id: "p4",
    brand: "SkinSage",
    name: "10% Niacinamide Booster",
    category: "Serum",
    price: 1099,
    rating: 4.7,
    image: "https://images.unsplash.com/photo-1629729927228-4f6a2a1a8f1f?q=80&w=800&auto=format&fit=crop",
    shades: [],
    ingredients: [
      "Aqua",
      "Niacinamide",
      "Zinc PCA",
      "Propylene Glycol",
      "Sodium Hyaluronate",
      "Panthenol",
      "Phenoxyethanol",
    ],
    tags: ["brightening", "pore-care"],
  },
];

const DUPES = [
  // Map of productId -> array of dupe options
  {
    base: "p1",
    dupes: [
      {
        id: "d1",
        brand: "Serumly",
        name: "Bright 10 Serum",
        price: 899,
        image: "https://images.unsplash.com/photo-1582095133179-bfd08e2fc6b3?q=80&w=800&auto=format&fit=crop",
        ingredients: [
          "Aqua",
          "Niacinamide",
          "Glycerin",
          "Panthenol",
          "Sodium Hyaluronate",
          "Allantoin",
          "Phenoxyethanol",
        ],
      },
      {
        id: "d2",
        brand: "BareLab",
        name: "Refine Booster",
        price: 749,
        image: "https://images.unsplash.com/photo-1556228720-195a672e8a03?q=80&w=800&auto=format&fit=crop",
        ingredients: [
          "Water",
          "Niacinamide",
          "Glycerin",
          "Butylene Glycol",
          "Sodium Hyaluronate",
          "Phenoxyethanol",
        ],
      },
    ],
  },
  {
    base: "p2",
    dupes: [
      {
        id: "d3",
        brand: "MatteMuse",
        name: "Cloud Kiss",
        price: 499,
        image: "https://images.unsplash.com/photo-1563170351-be82bc888aa4?q=80&w=800&auto=format&fit=crop",
        ingredients: [
          "Castor Seed Oil",
          "Candelilla Wax",
          "Isododecane",
          "Silica",
          "Tocopherol",
          "Red 7 Lake",
        ],
      },
    ],
  },
];

// ---------------------------------------------
// Utilities
// ---------------------------------------------
function normalizeINCI(str = "") {
  return str
    .toLowerCase()
    .replace(/\([^)]*\)/g, "") // drop parenthetical
    .replace(/[^a-z0-9\s]/g, " ")
    .replace(/\s+/g, " ")
    .trim();
}

function tokenize(list) {
  return list
    .map(normalizeINCI)
    .flatMap((s) => s.split(","))
    .flatMap((s) => s.split(" "))
    .map((s) => s.trim())
    .filter(Boolean);
}

function ingredientSimilarity(aList, bList) {
  const A = new Set(tokenize(aList));
  const B = new Set(tokenize(bList));
  if (!A.size || !B.size) return 0;
  let inter = 0;
  for (const t of A) if (B.has(t)) inter++;
  const union = new Set([...A, ...B]).size;
  return inter / union; // Jaccard-like
}

function priceDiff(basePrice, dupePrice) {
  return Math.round(((basePrice - dupePrice) / basePrice) * 100);
}

// Chip component
function Chip({ children }) {
  return (
    <span className="px-2 py-1 rounded-full bg-muted text-muted-foreground text-xs">
      {children}
    </span>
  );
}

// ---------------------------------------------
// Main App
// ---------------------------------------------
export default function MakeupDupesStore() {
  const [query, setQuery] = useState("");
  const [category, setCategory] = useState("All");
  const [sheetOpen, setSheetOpen] = useState(false);
  const [activeProduct, setActiveProduct] = useState(null);
  const [ingredientPaste, setIngredientPaste] = useState("");

  const filtered = useMemo(() => {
    return PRODUCTS.filter((p) => {
      const matchesQuery = (p.name + " " + p.brand + " " + p.category)
        .toLowerCase()
        .includes(query.toLowerCase());
      const matchesCat = category === "All" || p.category === category;
      return matchesQuery && matchesCat;
    });
  }, [query, category]);

  const categories = useMemo(() => ["All", ...Array.from(new Set(PRODUCTS.map((p) => p.category)))], []);

  function openDupes(product) {
    setActiveProduct(product);
    setSheetOpen(true);
  }

  const ingredientMatches = useMemo(() => {
    if (!ingredientPaste.trim()) return [];
    return PRODUCTS.map((p) => ({
      product: p,
      score: ingredientSimilarity([ingredientPaste], p.ingredients),
    }))
      .sort((a, b) => b.score - a.score)
      .slice(0, 5);
  }, [ingredientPaste]);

  return (
    <div className="min-h-screen w-full bg-gradient-to-b from-white to-slate-50 text-slate-900">
      {/* Header */}
      <header className="sticky top-0 z-30 backdrop-blur bg-white/70 border-b border-slate-200">
        <div className="mx-auto max-w-7xl px-4 py-3 flex items-center gap-3">
          <Sparkles className="w-6 h-6" />
          <h1 className="text-xl font-semibold tracking-tight">DupeDeck · Makeup & Skincare</h1>
          <div className="ml-auto flex items-center gap-2">
            <Input
              value={query}
              onChange={(e) => setQuery(e.target.value)}
              placeholder="Search products, brands, categories"
              className="w-72"
            />
            <Button variant="secondary"><Search className="w-4 h-4 mr-2"/>Search</Button>
            <DropdownMenu>
              <DropdownMenuTrigger asChild>
                <Button variant="outline"><SlidersHorizontal className="w-4 h-4 mr-2"/>Filter</Button>
              </DropdownMenuTrigger>
              <DropdownMenuContent align="end" className="w-48">
                <DropdownMenuLabel>Category</DropdownMenuLabel>
                <DropdownMenuSeparator />
                {categories.map((c) => (
                  <DropdownMenuItem key={c} onClick={() => setCategory(c)}>
                    {c}
                    {category === c && <Check className="ml-auto w-4 h-4"/>}
                  </DropdownMenuItem>
                ))}
              </DropdownMenuContent>
            </DropdownMenu>
            <Button className="ml-2" variant="default"><ShoppingCart className="w-4 h-4 mr-2"/>Cart</Button>
          </div>
        </div>
      </header>

      {/* Hero */}
      <section className="mx-auto max-w-7xl px-4 py-10">
        <div className="grid md:grid-cols-3 gap-4">
          <Card className="md:col-span-2 bg-gradient-to-r from-fuchsia-50 to-sky-50">
            <CardHeader>
              <CardTitle className="flex items-center gap-2">
                <FlaskConical className="w-5 h-5"/> Find Ingredient-Based Dupes
              </CardTitle>
            </CardHeader>
            <CardContent>
              <p className="text-sm text-slate-600 mb-3">Paste an INCI list and discover products with similar compositions. We compute a Jaccard-style overlap score on normalized ingredients.</p>
              <textarea
                className="w-full min-h-[120px] p-3 border rounded-2xl bg-white focus:outline-none"
                placeholder="Example: Aqua, Niacinamide, Glycerin, Sodium Hyaluronate, Panthenol, Allantoin"
                value={ingredientPaste}
                onChange={(e) => setIngredientPaste(e.target.value)}
              />
              <div className="mt-3 flex items-center gap-2">
                <Badge variant="secondary">Private · On-device</Badge>
                <Badge variant="outline">Beta</Badge>
              </div>
              {/* Results */}
              <AnimatePresence>
                {ingredientPaste && (
                  <motion.div
                    initial={{ opacity: 0, y: 8 }}
                    animate={{ opacity: 1, y: 0 }}
                    exit={{ opacity: 0, y: 8 }}
                    className="mt-5 grid sm:grid-cols-2 gap-3"
                  >
                    {ingredientMatches.map(({ product, score }) => (
                      <Card key={product.id} className="hover:shadow-lg transition-shadow cursor-pointer" onClick={() => openDupes(product)}>
                        <CardContent className="p-4 flex gap-3">
                          <img src={product.image} alt={product.name} className="w-20 h-20 object-cover rounded-xl" />
                          <div className="flex-1">
                            <div className="text-sm font-medium">{product.brand}</div>
                            <div className="text-base font-semibold leading-tight">{product.name}</div>
                            <div className="text-xs text-slate-500 mt-1">Similarity · {(score * 100).toFixed(0)}%</div>
                          </div>
                          <Button size="sm" variant="secondary">View dupes</Button>
                        </CardContent>
                      </Card>
                    ))}
                  </motion.div>
                )}
              </AnimatePresence>
            </CardContent>
          </Card>

          <Card>
            <CardHeader>
              <CardTitle>Quick Filters</CardTitle>
            </CardHeader>
            <CardContent className="flex flex-wrap gap-2">
              {[
                "fragrance-free",
                "non-comedogenic",
                "long-wear",
                "oil-free",
                "budget < ₹1000",
              ].map((f) => (
                <Chip key={f}>{f}</Chip>
              ))}
            </CardContent>
          </Card>
        </div>
      </section>

      {/* Product Grid */}
      <section className="mx-auto max-w-7xl px-4 pb-16">
        <div className="flex items-end justify-between mb-4">
          <h2 className="text-lg font-semibold">Trending Products</h2>
          <div className="text-sm text-slate-500">{filtered.length} results</div>
        </div>
        <div className="grid sm:grid-cols-2 lg:grid-cols-3 gap-4">
          {filtered.map((p) => (
            <motion.div key={p.id} initial={{ opacity: 0, y: 10 }} animate={{ opacity: 1, y: 0 }}>
              <Card className="overflow-hidden group">
                <div className="aspect-[4/3] overflow-hidden">
                  <img src={p.image} alt={p.name} className="w-full h-full object-cover group-hover:scale-105 transition-transform" />
                </div>
                <CardContent className="p-4">
                  <div className="text-xs text-slate-500">{p.brand} · {p.category}</div>
                  <div className="font-semibold leading-tight mt-0.5">{p.name}</div>
                  <div className="flex items-center gap-2 mt-2">
                    {p.tags.slice(0,3).map(t => <Badge key={t} variant="secondary">{t}</Badge>)}
                  </div>
                  <div className="mt-3 flex items-center justify-between">
                    <div className="font-bold">₹{p.price}</div>
                    <div className="text-sm">⭐ {p.rating}</div>
                  </div>
                  <div className="mt-3 flex gap-2">
                    <Button className="flex-1" onClick={() => openDupes(p)} variant="outline">Find Dupes</Button>
                    <Button className="flex-1" variant="default">Add to Cart</Button>
                  </div>
                </CardContent>
              </Card>
            </motion.div>
          ))}
        </div>
      </section>

      {/* Dupe Drawer */}
      <Sheet open={sheetOpen} onOpenChange={setSheetOpen}>
        <SheetContent side="right" className="w-full sm:max-w-xl overflow-y-auto">
          <SheetHeader>
            <SheetTitle>Dupes & Ingredient Match</SheetTitle>
          </SheetHeader>

          {activeProduct && (
            <div className="mt-5">
              <div className="flex gap-3 items-start">
                <img src={activeProduct.image} alt={activeProduct.name} className="w-24 h-24 rounded-xl object-cover"/>
                <div>
                  <div className="text-xs text-slate-500">{activeProduct.brand} · {activeProduct.category}</div>
                  <div className="font-semibold text-lg">{activeProduct.name}</div>
                  <div className="text-sm mt-1">₹{activeProduct.price} · ⭐ {activeProduct.rating}</div>
                </div>
              </div>

              <Tabs defaultValue="dupes" className="mt-5">
                <TabsList className="grid grid-cols-3">
                  <TabsTrigger value="dupes">Dupes</TabsTrigger>
                  <TabsTrigger value="ingredients">Ingredients</TabsTrigger>
                  <TabsTrigger value="compare">Compare</TabsTrigger>
                </TabsList>

                <TabsContent value="dupes" className="mt-4 space-y-3">
                  {(
                    DUPES.find((d) => d.base === activeProduct.id)?.dupes || []
                  ).map((d) => {
                    const sim = ingredientSimilarity(activeProduct.ingredients, d.ingredients);
                    const diff = priceDiff(activeProduct.price, d.price);
                    return (
                      <Card key={d.id} className="hover:shadow-md transition-shadow">
                        <CardContent className="p-4 flex gap-3">
                          <img src={d.image} alt={d.name} className="w-20 h-20 object-cover rounded-xl"/>
                          <div className="flex-1">
                            <div className="text-sm text-slate-500">{d.brand}</div>
                            <div className="font-semibold leading-tight">{d.name}</div>
                            <div className="text-xs text-slate-500 mt-1">Similarity · {(sim * 100).toFixed(0)}%</div>
                            <div className="text-xs mt-1 flex flex-wrap gap-1">
                              {Array.from(new Set(tokenize(d.ingredients))).slice(0,6).map((ing) => (
                                <Chip key={ing}>{ing}</Chip>
                              ))}
                            </div>
                          </div>
                          <div className="text-right">
                            <div className="font-semibold">₹{d.price}</div>
                            <div className={`text-xs mt-1 ${diff > 0 ? "text-emerald-600" : "text-slate-500"}`}>
                              {diff > 0 ? `${diff}% cheaper` : "Similar price"}
                            </div>
                            <Button size="sm" className="mt-2">Add</Button>
                          </div>
                        </CardContent>
                      </Card>
                    );
                  })}
                  {(
                    DUPES.find((d) => d.base === activeProduct.id)?.dupes || []
                  ).length === 0 && (
                    <div className="text-sm text-slate-500">No curated dupes yet. Try the Ingredients tab to discover similar formulas.</div>
                  )}
                </TabsContent>

                <TabsContent value="ingredients" className="mt-4">
                  <Card>
                    <CardHeader>
                      <CardTitle className="text-base">Base Formula (INCI)</CardTitle>
                    </CardHeader>
                    <CardContent className="flex flex-wrap gap-2">
                      {activeProduct.ingredients.map((ing) => (
                        <Badge key={ing} variant="outline">{ing}</Badge>
                      ))}
                    </CardContent>
                  </Card>
                </TabsContent>

                <TabsContent value="compare" className="mt-4">
                  <p className="text-sm text-slate-600 mb-3">Side-by-side overlap of top dupes with the base product.</p>
                  <div className="space-y-3">
                    {(
                      DUPES.find((d) => d.base === activeProduct.id)?.dupes || []
                    ).map((d) => {
                      const sim = ingredientSimilarity(activeProduct.ingredients, d.ingredients);
                      return (
                        <div key={d.id} className="p-3 border rounded-2xl">
                          <div className="flex items-center justify-between">
                            <div className="font-medium">{d.brand} · {d.name}</div>
                            <div className="text-sm">Similarity {(sim * 100).toFixed(0)}%</div>
                          </div>
                          <div className="grid md:grid-cols-2 gap-2 mt-2 text-sm">
                            <div>
                              <div className="text-slate-500 mb-1">Common ingredients</div>
                              <div className="flex flex-wrap gap-1">
                                {Array.from(new Set(tokenize(activeProduct.ingredients))).filter((t) => new Set(tokenize(d.ingredients)).has(t)).slice(0,12).map((t) => (
                                  <Chip key={t}>{t}</Chip>
                                ))}
                              </div>
                            </div>
                            <div>
                              <div className="text-slate-500 mb-1">Unique to dupe</div>
                              <div className="flex flex-wrap gap-1">
                                {Array.from(new Set(tokenize(d.ingredients))).filter((t) => !new Set(tokenize(activeProduct.ingredients)).has(t)).slice(0,12).map((t) => (
                                  <Chip key={t}>{t}</Chip>
                                ))}
                              </div>
                            </div>
                          </div>
                        </div>
                      );
                    })}
                  </div>
                </TabsContent>
              </Tabs>
            </div>
          )}
        </SheetContent>
      </Sheet>

      {/* Footer */}
      <footer className="border-t border-slate-200 bg-white/70">
        <div className="mx-auto max-w-7xl px-4 py-8 text-sm text-slate-500 flex flex-wrap gap-3 items-center justify-between">
          <div>© {new Date().getFullYear()} DupeDeck. For demo only.</div>
          <div className="flex gap-2 items-center">
            <Badge variant="outline">Made with React · Tailwind · shadcn/ui</Badge>
          </div>
        </div>
      </footer>
    </div>
  );
}
