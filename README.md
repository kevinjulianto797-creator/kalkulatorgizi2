import { useState, useEffect } from 'react';
import { Search, Filter, Plus, Minus, Calculator, Globe, TrendingUp, Share, Download, Trash2 } from 'lucide-react';

interface FoodItem {
  id: string;
  name: string;
  category: string;
  area: string;
  calories: number;
  protein: number;
  carbs: number;
  fat: number;
  fiber: number;
  sugar: number;
  sodium: number;
}

interface SelectedFood {
  food: FoodItem;
  quantity: number;
}

const NutritionCalculator = () => {
  const [searchTerm, setSearchTerm] = useState('');
  const [selectedCategory, setSelectedCategory] = useState('all');
  const [selectedArea, setSelectedArea] = useState('all');
  const [selectedFoods, setSelectedFoods] = useState<SelectedFood[]>([]);
  const [isSearchFocused, setIsSearchFocused] = useState(false);
  const [showShareModal, setShowShareModal] = useState(false);
  const [shareUrl, setShareUrl] = useState('');

  // Database makanan dengan prioritas Indonesia
  const foodDatabase: FoodItem[] = [
    // Makanan Indonesia
    {
      id: '1',
      name: 'Nasi Putih',
      category: 'makanan-pokok',
      area: 'Indonesia',
      calories: 130,
      protein: 2.7,
      carbs: 28,
      fat: 0.3,
      fiber: 0.4,
      sugar: 0.1,
      sodium: 1
    },
    {
      id: '2',
      name: 'Rendang',
      category: 'lauk-pauk',
      area: 'Indonesia',
      calories: 193,
      protein: 25,
      carbs: 2,
      fat: 9,
      fiber: 0.8,
      sugar: 0.5,
      sodium: 450
    },
    {
      id: '3',
      name: 'Gado-gado',
      category: 'sayuran',
      area: 'Indonesia',
      calories: 180,
      protein: 8,
      carbs: 25,
      fat: 6,
      fiber: 5,
      sugar: 8,
      sodium: 320
    },
    {
      id: '4',
      name: 'Sate Ayam',
      category: 'lauk-pauk',
      area: 'Indonesia',
      calories: 150,
      protein: 20,
      carbs: 3,
      fat: 6,
      fiber: 0.2,
      sugar: 2,
      sodium: 380
    },
    {
      id: '5',
      name: 'Soto Ayam',
      category: 'sup',
      area: 'Indonesia',
      calories: 120,
      protein: 15,
      carbs: 8,
      fat: 4,
      fiber: 1.2,
      sugar: 2,
      sodium: 520
    },
    {
      id: '6',
      name: 'Bakso',
      category: 'sup',
      area: 'Indonesia',
      calories: 250,
      protein: 18,
      carbs: 20,
      fat: 8,
      fiber: 1.5,
      sugar: 3,
      sodium: 480
    },
    {
      id: '7',
      name: 'Nasi Goreng',
      category: 'makanan-utama',
      area: 'Indonesia',
      calories: 350,
      protein: 12,
      carbs: 45,
      fat: 12,
      fiber: 2.5,
      sugar: 5,
      sodium: 680
    },

    // Makanan Asia
    {
      id: '8',
      name: 'Sushi Salmon',
      category: 'makanan-utama',
      area: 'Jepang',
      calories: 45,
      protein: 6,
      carbs: 7,
      fat: 1,
      fiber: 0.3,
      sugar: 0.8,
      sodium: 85
    },
    {
      id: '9',
      name: 'Ramen',
      category: 'sup',
      area: 'Jepang',
      calories: 450,
      protein: 20,
      carbs: 55,
      fat: 15,
      fiber: 3,
      sugar: 4,
      sodium: 1200
    },
    {
      id: '10',
      name: 'Pad Thai',
      category: 'makanan-utama',
      area: 'Thailand',
      calories: 380,
      protein: 15,
      carbs: 60,
      fat: 10,
      fiber: 3,
      sugar: 12,
      sodium: 890
    },
    {
      id: '11',
      name: 'Kimchi',
      category: 'sayuran',
      area: 'Korea',
      calories: 25,
      protein: 2,
      carbs: 5,
      fat: 0.5,
      fiber: 2,
      sugar: 2,
      sodium: 500
    },

    // Makanan Barat
    {
      id: '12',
      name: 'Pizza Margherita',
      category: 'makanan-utama',
      area: 'Italia',
      calories: 285,
      protein: 12,
      carbs: 36,
      fat: 10,
      fiber: 2.5,
      sugar: 3.5,
      sodium: 640
    },
    {
      id: '13',
      name: 'Hamburger',
      category: 'makanan-utama',
      area: 'Amerika',
      calories: 354,
      protein: 20,
      carbs: 29,
      fat: 17,
      fiber: 1.8,
      sugar: 5,
      sodium: 530
    },
    {
      id: '14',
      name: 'Spaghetti',
      category: 'makanan-utama',
      area: 'Italia',
      calories: 220,
      protein: 8,
      carbs: 42,
      fat: 2,
      fiber: 2.5,
      sugar: 2,
      sodium: 280
    },
    {
      id: '15',
      name: 'Tacos',
      category: 'makanan-utama',
      area: 'Meksiko',
      calories: 220,
      protein: 12,
      carbs: 25,
      fat: 8,
      fiber: 3,
      sugar: 2,
      sodium: 420
    },

    // Buah-buahan dan Lainnya
    {
      id: '16',
      name: 'Pisang',
      category: 'buah',
      area: 'Tropis',
      calories: 89,
      protein: 1.1,
      carbs: 23,
      fat: 0.3,
      fiber: 2.6,
      sugar: 12,
      sodium: 1
    },
    {
      id: '17',
      name: 'Apel',
      category: 'buah',
      area: 'Global',
      calories: 52,
      protein: 0.3,
      carbs: 14,
      fat: 0.2,
      fiber: 2.4,
      sugar: 10,
      sodium: 1
    },
    {
      id: '18',
      name: 'Ayam Goreng',
      category: 'lauk-pauk',
      area: 'Global',
      calories: 280,
      protein: 25,
      carbs: 8,
      fat: 16,
      fiber: 0.5,
      sugar: 0,
      sodium: 420
    },
    {
      id: '19',
      name: 'Telur Rebus',
      category: 'lauk-pauk',
      area: 'Global',
      calories: 155,
      protein: 13,
      carbs: 1.1,
      fat: 11,
      fiber: 0,
      sugar: 1.1,
      sodium: 124
    },
    {
      id: '20',
      name: 'Roti Tawar',
      category: 'makanan-pokok',
      area: 'Global',
      calories: 265,
      protein: 9,
      carbs: 49,
      fat: 3.2,
      fiber: 2.7,
      sugar: 5,
      sodium: 491
    }
  ];

  const categories = ['all', 'makanan-pokok', 'lauk-pauk', 'sayuran', 'sup', 'makanan-utama', 'buah'];
  const areas = ['all', 'Indonesia', 'Jepang', 'Thailand', 'Korea', 'Italia', 'Amerika', 'Meksiko', 'Global', 'Tropis'];

  const filteredFoods = foodDatabase.filter(food => {
    const matchesSearch = food.name.toLowerCase().includes(searchTerm.toLowerCase());
    const matchesCategory = selectedCategory === 'all' || food.category === selectedCategory;
    const matchesArea = selectedArea === 'all' || food.area === selectedArea;
    
    return matchesSearch && matchesCategory && matchesArea;
  });

  const addFoodToCalculator = (food: FoodItem) => {
    const existingFood = selectedFoods.find(item => item.food.id === food.id);
    
    if (existingFood) {
      setSelectedFoods(selectedFoods.map(item =>
        item.food.id === food.id
          ? { ...item, quantity: item.quantity + 1 }
          : item
      ));
    } else {
      setSelectedFoods([...selectedFoods, { food, quantity: 1 }]);
    }
    setSearchTerm('');
  };

  const updateQuantity = (id: string, newQuantity: number) => {
    if (newQuantity <= 0) {
      setSelectedFoods(selectedFoods.filter(item => item.food.id !== id));
    } else {
      setSelectedFoods(selectedFoods.map(item =>
        item.food.id === id
          ? { ...item, quantity: newQuantity }
          : item
      ));
    }
  };

  const calculateTotals = () => {
    return selectedFoods.reduce((totals, item) => ({
      calories: totals.calories + (item.food.calories * item.quantity),
      protein: totals.protein + (item.food.protein * item.quantity),
      carbs: totals.carbs + (item.food.carbs * item.quantity),
      fat: totals.fat + (item.food.fat * item.quantity),
      fiber: totals.fiber + (item.food.fiber * item.quantity),
      sugar: totals.sugar + (item.food.sugar * item.quantity),
      sodium: totals.sodium + (item.food.sodium * item.quantity),
    }), {
      calories: 0,
      protein: 0,
      carbs: 0,
      fat: 0,
      fiber: 0,
      sugar: 0,
      sodium: 0
    });
  };

  const totals = calculateTotals();

  // Generate shareable URL
  const generateShareUrl = () => {
    const foodIds = selectedFoods.map(item => `${item.food.id}:${item.quantity}`).join(',');
    const url = `${window.location.origin}${window.location.pathname}?foods=${foodIds}`;
    setShareUrl(url);
    setShowShareModal(true);
  };

  // Load from URL parameters
  useEffect(() => {
    const urlParams = new URLSearchParams(window.location.search);
    const foodsParam = urlParams.get('foods');
    
    if (foodsParam) {
      const foodItems = foodsParam.split(',');
      const loadedFoods: SelectedFood[] = [];
      
      foodItems.forEach(item => {
        const [id, quantity] = item.split(':');
        const food = foodDatabase.find(f => f.id === id);
        if (food) {
          loadedFoods.push({ food, quantity: parseInt(quantity) || 1 });
        }
      });
      
      if (loadedFoods.length > 0) {
        setSelectedFoods(loadedFoods);
      }
    }
  }, []);

  // Export as CSV
  const exportToCSV = () => {
    const csvContent = [
      ['Makanan', 'Jumlah', 'Kalori', 'Protein (g)', 'Karbohidrat (g)', 'Lemak (g)', 'Serat (g)', 'Gula (g)', 'Sodium (mg)'],
      ...selectedFoods.map(({ food, quantity }) => [
        food.name,
        quantity,
        food.calories * quantity,
        food.protein * quantity,
        food.carbs * quantity,
        food.fat * quantity,
        food.fiber * quantity,
        food.sugar * quantity,
        food.sodium * quantity
      ]),
      ['TOTAL', '', totals.calories, totals.protein, totals.carbs, totals.fat, totals.fiber, totals.sugar, totals.sodium]
    ].map(row => row.join(',')).join('\n');

    const blob = new Blob([csvContent], { type: 'text/csv' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'nutrisi-makanan.csv';
    a.click();
    URL.revokeObjectURL(url);
  };

  // Clear all selected foods
  const clearAllFoods = () => {
    setSelectedFoods([]);
  };

  return (
    <div className="min-h-screen bg-background p-4 md:p-8">
      <div className="max-w-6xl mx-auto">
        {/* Header */}
        <div className="text-center mb-8">
          <h1 className="text-3xl md:text-4xl font-bold text-primary mb-2">
            Kalkulator Gizi Makanan
          </h1>
          <div className="flex items-center justify-center gap-2 text-muted-foreground mb-4">
            <Globe className="h-5 w-5" />
            <p>Hitung nilai gizi dari makanan favorit Anda dari seluruh dunia</p>
          </div>
          <div className="flex items-center justify-center gap-4">
            <span className="inline-flex items-center gap-1 bg-accent/50 px-3 py-1 rounded-full text-sm">
              <TrendingUp className="h-4 w-4" />
              {foodDatabase.length}+ makanan tersedia
            </span>
            <span className="inline-flex items-center gap-1 bg-accent/50 px-3 py-1 rounded-full text-sm">
              🌍 Multi-bahasa
            </span>
            <span className="inline-flex items-center gap-1 bg-accent/50 px-3 py-1 rounded-full text-sm">
              📱 Responsif
            </span>
          </div>
        </div>

        <div className="grid grid-cols-1 lg:grid-cols-3 gap-8">
          {/* Search and Filter Section */}
          <div className="lg:col-span-2">
            <div className="bg-card rounded-lg p-6 shadow-sm border">
              <div className="relative mb-6">
                <div className="relative">
                  <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 text-muted-foreground h-5 w-5" />
                  <input
                    type="text"
                    placeholder="Cari makanan..."
                    value={searchTerm}
                    onChange={(e) => setSearchTerm(e.target.value)}
                    onFocus={() => setIsSearchFocused(true)}
                    onBlur={() => setTimeout(() => setIsSearchFocused(false), 200)}
                    className="w-full pl-10 pr-4 py-3 border border-border rounded-lg focus:ring-2 focus:ring-primary focus:border-transparent"
                  />
                </div>

                {/* Search suggestions */}
                {isSearchFocused && searchTerm && (
                  <div className="absolute z-10 w-full mt-1 bg-card border border-border rounded-lg shadow-lg max-h-60 overflow-y-auto">
                    {filteredFoods.slice(0, 5).map((food) => (
                      <div
                        key={food.id}
                        className="px-4 py-3 hover:bg-accent cursor-pointer border-b border-border last:border-b-0"
                        onClick={() => addFoodToCalculator(food)}
                      >
                        <div className="font-medium">{food.name}</div>
                        <div className="text-sm text-muted-foreground">
                          {food.area} • {food.calories} kalori
                        </div>
                      </div>
                    ))}
                  </div>
                )}
              </div>

              <div className="grid grid-cols-2 gap-4 mb-6">
                <div>
                  <label className="block text-sm font-medium text-foreground mb-2">
                    Kategori
                  </label>
                  <select
                    value={selectedCategory}
                    onChange={(e) => setSelectedCategory(e.target.value)}
                    className="w-full px-3 py-2 border border-border rounded-lg focus:ring-2 focus:ring-primary"
                  >
                    {categories.map(category => (
                      <option key={category} value={category}>
                        {category === 'all' ? 'Semua Kategori' : 
                         category === 'makanan-pokok' ? 'Makanan Pokok' :
                         category === 'lauk-pauk' ? 'Lauk Pauk' :
                         category === 'sayuran' ? 'Sayuran' :
                         category === 'sup' ? 'Sup' :
                         category === 'buah' ? 'Buah-buahan' : 'Makanan Utama'}
                      </option>
                    ))}
                  </select>
                </div>

                <div>
                  <label className="block text-sm font-medium text-foreground mb-2">
                    Daerah
                  </label>
                  <select
                    value={selectedArea}
                    onChange={(e) => setSelectedArea(e.target.value)}
                    className="w-full px-3 py-2 border border-border rounded-lg focus:ring-2 focus:ring-primary"
                  >
                    {areas.map(area => (
                      <option key={area} value={area}>
                        {area === 'all' ? 'Semua Daerah' : area}
                      </option>
                    ))}
                  </select>
                </div>
              </div>

              {/* Food Grid */}
              <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                {filteredFoods.map((food) => (
                  <div
                    key={food.id}
                    className="border border-border rounded-lg p-4 hover:shadow-md transition-shadow cursor-pointer"
                    onClick={() => addFoodToCalculator(food)}
                  >
                    <div className="flex items-start gap-4">
                      <div className="w-16 h-16 bg-muted rounded-lg flex items-center justify-center">
                        <img 
                          src="https://placeholder-image-service.onrender.com/image/64x64?prompt=Indonesian%20food%20item%20on%20a%20white%20plate&id=food-thumbnail-1" 
                          alt={`Gambar ${food.name} yang disajikan di piring putih`}
                          className="w-full h-full object-cover rounded-lg"
                        />
                      </div>
                      <div className="flex-1">
                        <h3 className="font-semibold text-foreground">{food.name}</h3>
                        <p className="text-sm text-muted-foreground mb-2">{food.area}</p>
                        <div className="grid grid-cols-3 gap-2 text-xs">
                  <span className="text-primary">{food.calories} kal</span>
                  <span className="text-blue-600">{food.protein}g prot</span>
                  <span className="text-green-600">{food.carbs}g karbo</span>
                </div>
                <div className="mt-1">
                  <span className="inline-block bg-muted px-2 py-1 rounded text-xs text-muted-foreground">
                    {food.area}
                  </span>
                </div>
                      </div>
                    </div>
                  </div>
                ))}
              </div>
            </div>
          </div>

          {/* Calculator Sidebar */}
          <div className="bg-card rounded-lg p-6 shadow-sm border h-fit">
            <div className="flex items-center gap-2 mb-6">
              <Calculator className="h-6 w-6 text-primary" />
              <h2 className="text-xl font-semibold text-foreground">Kalkulator Gizi</h2>
            </div>

            {selectedFoods.length === 0 ? (
              <div className="text-center py-8 text-muted-foreground">
                <p>Pilih makanan untuk mulai menghitung</p>
              </div>
            ) : (
              <>
                <div className="space-y-4 mb-6">
                  {selectedFoods.map(({ food, quantity }) => (
                    <div key={food.id} className="flex items-center justify-between p-3 border border-border rounded-lg">
                      <div className="flex-1">
                        <div className="font-medium text-foreground">{food.name}</div>
                        <div className="text-sm text-muted-foreground">{food.calories * quantity} kalori</div>
                      </div>
                      <div className="flex items-center gap-2">
                        <button
                          onClick={() => updateQuantity(food.id, quantity - 1)}
                          className="p-1 rounded-md hover:bg-accent"
                        >
                          <Minus className="h-4 w-4" />
                        </button>
                        <span className="w-8 text-center font-medium">{quantity}</span>
                        <button
                          onClick={() => updateQuantity(food.id, quantity + 1)}
                          className="p-1 rounded-md hover:bg-accent"
                        >
                          <Plus className="h-4 w-4" />
                        </button>
                      </div>
                    </div>
                  ))}
                </div>

                {/* Total Nutrition */}
                <div className="border-t border-border pt-6">
                  <h3 className="font-semibold text-foreground mb-4">Total Gizi</h3>
                  <div className="grid grid-cols-2 gap-4 text-sm mb-6">
                    <div>
                      <span className="text-muted-foreground">Kalori:</span>
                      <span className="float-right font-medium text-primary">{totals.calories}</span>
                    </div>
                    <div>
                      <span className="text-muted-foreground">Protein:</span>
                      <span className="float-right font-medium text-blue-600">{totals.protein}g</span>
                    </div>
                    <div>
                      <span className="text-muted-foreground">Karbohidrat:</span>
                      <span className="float-right font-medium text-green-600">{totals.carbs}g</span>
                    </div>
                    <div>
                      <span className="text-muted-foreground">Lemak:</span>
                      <span className="float-right font-medium text-orange-600">{totals.fat}g</span>
                    </div>
                    <div>
                      <span className="text-muted-foreground">Serat:</span>
                      <span className="float-right font-medium text-purple-600">{totals.fiber}g</span>
                    </div>
                    <div>
                      <span className="text-muted-foreground">Gula:</span>
                      <span className="float-right font-medium text-pink-600">{totals.sugar}g</span>
                    </div>
                    <div>
                      <span className="text-muted-foreground">Sodium:</span>
                      <span className="float-right font-medium text-yellow-600">{totals.sodium}mg</span>
                    </div>
                  </div>

                  {/* Action Buttons */}
                  <div className="flex gap-2">
                    <button
                      onClick={generateShareUrl}
                      className="flex-1 flex items-center justify-center gap-2 bg-primary text-primary-foreground py-2 px-4 rounded-lg hover:bg-primary/90 transition-colors"
                    >
                      <Share className="h-4 w-4" />
                      Bagikan
                    </button>
                    <button
                      onClick={exportToCSV}
                      className="flex-1 flex items-center justify-center gap-2 bg-secondary text-secondary-foreground py-2 px-4 rounded-lg hover:bg-secondary/90 transition-colors"
                    >
                      <Download className="h-4 w-4" />
                      Export
                    </button>
                    <button
                      onClick={clearAllFoods}
                      className="flex items-center justify-center gap-2 bg-destructive text-destructive-foreground py-2 px-4 rounded-lg hover:bg-destructive/90 transition-colors"
                    >
                      <Trash2 className="h-4 w-4" />
                    </button>
                  </div>
                </div>
              </>
            )}
          </div>
        </div>

        {/* Featured Indonesian Foods */}
        <div className="mt-12">
          <h2 className="text-2xl font-bold text-primary mb-6 text-center">
            Makanan Populer dari Berbagai Negara
          </h2>
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
            {foodDatabase
              .filter(food => food.area !== 'Global' && food.area !== 'Tropis')
              .slice(0, 8)
              .map(food => (
                <div
                  key={food.id}
                  className="bg-card rounded-lg p-4 shadow-sm border hover:shadow-md transition-shadow cursor-pointer"
                  onClick={() => addFoodToCalculator(food)}
                >
                  <div className="w-full h-48 bg-muted rounded-lg mb-4 overflow-hidden">
                    <img 
                      src={`https://placeholder-image-service.onrender.com/image/300x200?prompt=Traditional%20${food.area}%20${food.name}%20food%20presentation&id=international-food-${food.id}`}
                      alt={`Gambar tradisional ${food.name} dari ${food.area} yang disajikan dengan indah`}
                      className="w-full h-full object-cover"
                    />
                  </div>
                  <h3 className="font-semibold text-foreground mb-2">{food.name}</h3>
                  <div className="grid grid-cols-2 gap-2 text-sm">
                    <span className="text-primary">{food.calories} kal</span>
                    <span className="text-blue-600">{food.protein}g protein</span>
                  </div>
                </div>
              ))}
          </div>
        </div>
      </div>

      {/* Share Modal */}
      {showShareModal && (
        <div className="fixed inset-0 bg-black/50 flex items-center justify-center z-50">
          <div className="bg-card rounded-lg p-6 max-w-md w-full mx-4">
            <h3 className="text-lg font-semibold mb-4">Bagikan Perhitungan Gizi</h3>
            <p className="text-muted-foreground mb-4">
              Salin link berikut untuk berbagi perhitungan gizi Anda dengan orang lain:
            </p>
            <div className="flex gap-2 mb-4">
              <input
                type="text"
                value={shareUrl}
                readOnly
                className="flex-1 px-3 py-2 border border-border rounded-lg bg-muted"
              />
              <button
                onClick={() => {
                  navigator.clipboard.writeText(shareUrl);
                  alert('Link berhasil disalin!');
                }}
                className="bg-primary text-primary-foreground px-4 py-2 rounded-lg hover:bg-primary/90"
              >
                Salin
              </button>
            </div>
            <button
              onClick={() => setShowShareModal(false)}
              className="w-full bg-secondary text-secondary-foreground py-2 rounded-lg hover:bg-secondary/90"
            >
              Tutup
            </button>
          </div>
        </div>
      )}
    </div>
  );
};

export default NutritionCalculator;
