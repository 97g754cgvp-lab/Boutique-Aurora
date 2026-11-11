import React, { useState, useEffect } from 'react';
import { 
  ShoppingBag, 
  Sparkles, 
  Heart, 
  Search, 
  Trash2, 
  Plus, 
  Minus, 
  CheckCircle2, 
  RefreshCw, 
  Menu, 
  X, 
  Star,
  ChevronRight,
  ShieldCheck,
  Truck,
  RotateCcw
} from 'lucide-react';

// --- BASE DE DADOS DE PRODUTOS ---
const PRODUCTS = [
  {
    id: 1,
    name: "Vestido Midi Seda Rose",
    category: "Vestidos",
    price: 289.90,
    rating: 4.9,
    image: "https://images.unsplash.com/photo-1595777457583-95e059d581b8?auto=format&fit=crop&w=600&q=80",
    styleTag: "Elegante",
    description: "Vestido midi em seda acetinada, caimento fluido com decote degagê ideal para eventos noturnos ou jantares de gala."
  },
  {
    id: 2,
    name: "Blazer Linho Terracota Premium",
    category: "Tops & Corsets",
    price: 349.00,
    rating: 4.8,
    image: "https://images.unsplash.com/photo-1584273143981-41c073dfe8f8?auto=format&fit=crop&w=600&q=80",
    styleTag: "Moderno",
    description: "Blazer estruturado em mistura de linho de alfaiataria. Toque sofisticado para o ambiente corporativo e casual chic."
  },
  {
    id: 3,
    name: "Calça Pantalona Alfaiataria Nude",
    category: "Calças & Saia",
    price: 219.90,
    rating: 4.7,
    image: "https://images.unsplash.com/photo-1509631179647-0177331693ae?auto=format&fit=crop&w=600&q=80",
    styleTag: "Elegante",
    description: "Corte impecável de cintura alta, valoriza a silhueta com tecido encorpado e caimento reto."
  },
  {
    id: 4,
    name: "Corset Guipir Vintage Off-White",
    category: "Tops & Corsets",
    price: 179.90,
    rating: 4.9,
    image: "https://images.unsplash.com/photo-1515886657613-9f3515b0c78f?auto=format&fit=crop&w=600&q=80",
    styleTag: "Romântico",
    description: "Corset estruturado com barbatanas flexíveis e aplicação de renda delicada."
  },
  {
    id: 5,
    name: "Saia Plissada Metallic Blush",
    category: "Calças & Saia",
    price: 199.00,
    rating: 4.6,
    image: "https://images.unsplash.com/photo-1583496661160-fb5886a0aaaa?auto=format&fit=crop&w=600&q=80",
    styleTag: "Romântico",
    description: "Saia plissada midi com leve brilho acetinado, cós com elástico confortável."
  },
  {
    id: 6,
    name: "Bolsa Couro Matelassê Nude",
    category: "Acessórios",
    price: 259.90,
    rating: 4.9,
    image: "https://images.unsplash.com/photo-1584917865442-de89df76afd3?auto=format&fit=crop&w=600&q=80",
    styleTag: "Elegante",
    description: "Bolsa transversal em couro ecológico trabalhado em matelassê com alça em corrente dourada."
  },
  {
    id: 7,
    name: "Vestido Floral Boho Primavera",
    category: "Vestidos",
    price: 239.90,
    rating: 4.8,
    image: "https://images.unsplash.com/photo-1572804013309-59a88b7e92f1?auto=format&fit=crop&w=600&q=80",
    styleTag: "Romântico",
    description: "Estampa floral exclusiva, mangas bufantes e babados fluidos na barra."
  },
  {
    id: 8,
    name: "Brincos Gota Banhado Ouro 18k",
    category: "Acessórios",
    price: 89.90,
    rating: 5.0,
    image: "https://images.unsplash.com/photo-1535632066927-ab7c9ab60908?auto=format&fit=crop&w=600&q=80",
    styleTag: "Moderno",
    description: "Design botânico minimalista, hipoalergênicos e leve para uso diário."
  }
];

// --- PERGUNTAS DO QUIZ DE ESTILO ---
const QUIZ_QUESTIONS = [
  {
    id: 1,
    question: "Como você prefere se vestir no seu dia a dia?",
    options: [
      { label: "Peças clássicas, alfaiataria e tons neutros", style: "Elegante" },
      { label: "Vestidos fluidos, rendas e estampas florais", style: "Romântico" },
      { label: "Tendências urbanas, blazers estruturados e contraste", style: "Moderno" }
    ]
  },
  {
    id: 2,
    question: "Qual a sua paleta de cores favorita para um evento especial?",
    options: [
      { label: "Nude, bege, champagne e preto sofisticado", style: "Elegante" },
      { label: "Rosa seco, blush, lilás e tons pastéis", style: "Romântico" },
      { label: "Terracota, cobre, bordô e metálicos", style: "Moderno" }
    ]
  },
  {
    id: 3,
    question: "Qual acessório indispensável no seu look?",
    options: [
      { label: "Bolsa estruturada e joias discretas finas", style: "Elegante" },
      { label: "Tiara delicada, lenços de seda ou brincos florais", style: "Romântico" },
      { label: "Óculos escuros imponentes e mix de correntes", style: "Moderno" }
    ]
  }
];

export default function App() {
  // --- ESTADOS DA APLICAÇÃO (useState) ---
  const [activeTab, setActiveTab] = useState('store'); // 'store' | 'quiz' | 'cart'
  const [selectedCategory, setSelectedCategory] = useState('Todos');
  const [searchTerm, setSearchTerm] = useState('');
  const [cart, setCart] = useState([]);
  const [favorites, setFavorites] = useState([]);
  const [isMobileMenuOpen, setIsMobileMenuOpen] = useState(false);

  // Estados do Quiz
  const [quizAnswers, setQuizAnswers] = useState({});
  const [quizCompleted, setQuizCompleted] = useState(false);
  const [recommendedStyle, setRecommendedStyle] = useState(null);

  // --- PERSISTÊNCIA DE DADOS COM LOCALSTORAGE (useEffect) ---
  
  // 1. Carregar dados do localStorage ao iniciar o aplicativo
  useEffect(() => {
    try {
      const savedCart = localStorage.getItem('aura_cart');
      if (savedCart) setCart(JSON.parse(savedCart));

      const savedFavs = localStorage.getItem('aura_favorites');
      if (savedFavs) setFavorites(JSON.parse(savedFavs));

      const savedQuizResult = localStorage.getItem('aura_quiz_result');
      if (savedQuizResult) {
        const parsed = JSON.parse(savedQuizResult);
        setRecommendedStyle(parsed.style);
        setQuizCompleted(true);
      }
    } catch (error) {
      console.error("Erro ao carregar dados do localStorage:", error);
    }
  }, []);

  // 2. Salvar o Carrinho no localStorage quando houver mudanças
  useEffect(() => {
    localStorage.setItem('aura_cart', JSON.stringify(cart));
  }, [cart]);

  // 3. Salvar Favoritos no localStorage quando houver mudanças
  useEffect(() => {
    localStorage.setItem('aura_favorites', JSON.stringify(favorites));
  }, [favorites]);

  // --- LÓGICA DO CARRINHO ---
  const addToCart = (product) => {
    setCart(prev => {
      const exists = prev.find(item => item.id === product.id);
      if (exists) {
        return prev.map(item =>
          item.id === product.id ? { ...item, quantity: item.quantity + 1 } : item
        );
      }
      return [...prev, { ...product, quantity: 1 }];
    });
  };

  const updateQuantity = (id, delta) => {
    setCart(prev => 
      prev.map(item => {
        if (item.id === id) {
          const newQty = item.quantity + delta;
          return newQty > 0 ? { ...item, quantity: newQty } : item;
        }
        return item;
      })
    );
  };

  const removeFromCart = (id) => {
    setCart(prev => prev.filter(item => item.id !== id));
  };

  const clearCart = () => {
    setCart([]);
  };

  const cartTotal = cart.reduce((acc, item) => acc + (item.price * item.quantity), 0);
  const cartItemCount = cart.reduce((acc, item) => acc + item.quantity, 0);

  // --- LÓGICA DE FAVORITOS ---
  const toggleFavorite = (productId) => {
    setFavorites(prev => 
      prev.includes(productId) 
        ? prev.filter(id => id !== productId)
        : [...prev, productId]
    );
  };

  // --- LÓGICA DO QUIZ ---
  const handleAnswerSelect = (questionId, style) => {
    setQuizAnswers(prev => ({ ...prev, [questionId]: style }));
  };

  const calculateQuizResult = () => {
    const scores = { Elegante: 0, Romântico: 0, Moderno: 0 };
    Object.values(quizAnswers).forEach(style => {
      if (scores[style] !== undefined) {
        scores[style]++;
      }
    });

    let winningStyle = "Elegante";
    let maxScore = -1;
    Object.entries(scores).forEach(([style, score]) => {
      if (score > maxScore) {
        maxScore = score;
        winningStyle = style;
      }
    });

    setRecommendedStyle(winningStyle);
    setQuizCompleted(true);
    
    // Salva o resultado do Quiz no localStorage
    localStorage.setItem('aura_quiz_result', JSON.stringify({
      style: winningStyle,
      date: new Date().toLocaleDateString()
    }));
  };

  const resetQuiz = () => {
    setQuizAnswers({});
    setQuizCompleted(false);
    setRecommendedStyle(null);
    localStorage.removeItem('aura_quiz_result');
  };

  // --- FILTROS DE PRODUTOS ---
  const filteredProducts = PRODUCTS.filter(product => {
    const matchesCategory = selectedCategory === 'Todos' || product.category === selectedCategory;
    const matchesSearch = product.name.toLowerCase().includes(searchTerm.toLowerCase()) || 
                          product.description.toLowerCase().includes(searchTerm.toLowerCase());
    return matchesCategory && matchesSearch;
  });

  return (
    <div className="min-h-screen bg-[#FAF6F3] text-stone-800 font-sans flex flex-col selection:bg-rose-200">
      
      {/* CORPO DE NAVEGAÇÃO / HEADER */}
      <header className="sticky top-0 z-40 bg-white/90 backdrop-blur-md border-b border-rose-100 shadow-sm">
        {/* Banner Superior Promocional */}
        <div className="bg-[#B85B56] text-white text-xs py-1.5 text-center font-medium tracking-wider">
          ✦ FRETE GRÁTIS EM COMPRAS ACIMA DE R$ 299 | COUPO: AURA10 ✦
        </div>

        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="flex items-center justify-between h-20">
            
            {/* Logo */}
            <div className="flex items-center space-x-2 cursor-pointer" onClick={() => setActiveTab('store')}>
              <span className="font-serif text-3xl font-bold tracking-widest text-[#B85B56]">AURA</span>
              <span className="text-xs uppercase tracking-widest text-amber-800 font-light border-l border-stone-300 pl-2 hidden sm:inline">Boutique</span>
            </div>

            {/* Menu Desktop */}
            <nav className="hidden md:flex space-x-8 text-sm font-medium tracking-wide">
              <button 
                onClick={() => setActiveTab('store')}
                className={`transition-colors duration-200 py-1 ${activeTab === 'store' ? 'text-[#B85B56] border-b-2 border-[#B85B56]' : 'text-stone-600 hover:text-[#B85B56]'}`}
              >
                CATÁLOGO DE MODA
              </button>
              <button 
                onClick={() => setActiveTab('quiz')}
                className={`flex items-center gap-1.5 transition-colors duration-200 py-1 ${activeTab === 'quiz' ? 'text-[#B85B56] border-b-2 border-[#B85B56]' : 'text-stone-600 hover:text-[#B85B56]'}`}
              >
                <Sparkles className="w-4 h-4 text-amber-500" />
                QUIZ DE ESTILO
              </button>
            </nav>

            {/* Botões Direita (Busca, Favoritos, Carrinho) */}
            <div className="flex items-center space-x-5">
              
              {/* Botão de Carrinho */}
              <button 
                onClick={() => setActiveTab('cart')}
                className="relative p-2 text-stone-700 hover:text-[#B85B56] transition-colors"
                aria-label="Ver Carrinho"
              >
                <ShoppingBag className="w-6 h-6" />
                {cartItemCount > 0 && (
                  <span className="absolute top-0 right-0 inline-flex items-center justify-center px-2 py-1 text-xs font-bold leading-none text-white transform translate-x-1/2 -translate-y-1/2 bg-[#B85B56] rounded-full">
                    {cartItemCount}
                  </span>
                )}
              </button>

              {/* Botão Menu Mobile */}
              <button 
                onClick={() => setIsMobileMenuOpen(!isMobileMenuOpen)}
                className="md:hidden p-2 text-stone-700 hover:text-[#B85B56]"
              >
                {isMobileMenuOpen ? <X className="w-6 h-6" /> : <Menu className="w-6 h-6" />}
              </button>
            </div>
          </div>
        </div>

        {/* Menu Expandido Mobile */}
        {isMobileMenuOpen && (
          <div className="md:hidden bg-white border-b border-stone-200 px-4 pt-2 pb-4 space-y-2">
            <button 
              onClick={() => { setActiveTab('store'); setIsMobileMenuOpen(false); }}
              className="block w-full text-left px-3 py-2 text-stone-700 font-medium hover:bg-rose-50 rounded"
            >
              Catálogo de Moda
            </button>
            <button 
              onClick={() => { setActiveTab('quiz'); setIsMobileMenuOpen(false); }}
              className="flex items-center gap-2 w-full text-left px-3 py-2 text-stone-700 font-medium hover:bg-rose-50 rounded"
            >
              <Sparkles className="w-4 h-4 text-amber-500" />
              Quiz de Estilo Pessoal
            </button>
          </div>
        )}
      </header>

      {/* CONTEÚDO PRINCIPAL (Troca baseada na tab selecionada) */}
      <main className="flex-grow max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-8">
        
        {/* ================= ABA 1: LOJA / CATÁLOGO ================= */}
        {activeTab === 'store' && (
          <div>
            {/* Banner Principal / Hero */}
            <div className="relative rounded-2xl overflow-hidden bg-gradient-to-r from-[#D9A098] to-[#E8C4B8] text-stone-800 mb-10 p-8 sm:p-12 shadow-sm">
              <div className="max-w-xl space-y-4 relative z-10">
                <span className="inline-block bg-white/80 backdrop-blur-xs text-[#B85B56] text-xs font-semibold uppercase tracking-widest px-3 py-1 rounded-full">
                  Coleção Outono / Inverno 2026
                </span>
                <h1 className="font-serif text-3xl sm:text-5xl font-bold leading-tight text-stone-900">
                  Descubra a Elegância do Seu Próprio Estilo
                </h1>
                <p className="text-stone-700 text-sm sm:text-base leading-relaxed">
                  Peças atemporais com caimento perfeito, pensadas para expressar a sofisticação e autenticidade da mulher moderna.
                </p>
                <div className="pt-2">
                  <button 
                    onClick={() => setActiveTab('quiz')}
                    className="inline-flex items-center gap-2 bg-[#B85B56] hover:bg-[#a04e4a] text-white font-medium px-6 py-3 rounded-xl transition-all shadow-md hover:shadow-lg"
                  >
                    <Sparkles className="w-4 h-4" />
                    Descobrir Meu Estilo (Quiz)
                  </button>
                </div>
              </div>
            </div>

            {/* Barra de Filtros e Busca */}
            <div className="flex flex-col md:flex-row gap-4 justify-between items-center mb-8">
              {/* Categorias */}
              <div className="flex flex-wrap gap-2 justify-center md:justify-start w-full md:w-auto">
                {['Todos', 'Vestidos', 'Tops & Corsets', 'Calças & Saia', 'Acessórios'].map(cat => (
                  <button
                    key={cat}
                    onClick={() => setSelectedCategory(cat)}
                    className={`px-4 py-2 rounded-full text-sm font-medium transition-all ${
                      selectedCategory === cat 
                        ? 'bg-[#B85B56] text-white shadow-sm' 
                        : 'bg-white text-stone-600 hover:bg-stone-100 border border-stone-200'
                    }`}
                  >
                    {cat}
                  </button>
                ))}
              </div>

              {/* Input de Busca */}
              <div className="relative w-full md:w-72">
                <input 
                  type="text"
                  placeholder="Buscar peça ou estilo..."
                  value={searchTerm}
                  onChange={(e) => setSearchTerm(e.target.value)}
                  className="w-full pl-10 pr-4 py-2 bg-white border border-stone-200 rounded-full text-sm focus:outline-none focus:ring-2 focus:ring-[#B85B56]/50"
                />
                <Search className="absolute left-3.5 top-2.5 w-4 h-4 text-stone-400" />
              </div>
            </div>

            {/* Recomendação Ativa por causa do Quiz */}
            {recommendedStyle && (
              <div className="bg-rose-50 border border-rose-200 rounded-xl p-4 mb-8 flex items-center justify-between">
                <div className="flex items-center gap-3">
                  <Sparkles className="w-5 h-5 text-[#B85B56]" />
                  <p className="text-sm text-stone-800">
                    Seu estilo predileto é <strong className="text-[#B85B56]">{recommendedStyle}</strong>. Destacamos peças ideais para você!
                  </p>
                </div>
                <button 
                  onClick={() => setSearchTerm(recommendedStyle)}
                  className="text-xs bg-white text-[#B85B56] border border-rose-200 px-3 py-1.5 rounded-lg hover:bg-rose-100 font-medium"
                >
                  Filtrar Meu Estilo
                </button>
              </div>
            )}

            {/* Grid de Produtos */}
            {filteredProducts.length === 0 ? (
              <div className="text-center py-16 bg-white rounded-2xl border border-stone-100">
                <p className="text-stone-500 font-medium">Nenhum produto encontrado para sua busca.</p>
                <button 
                  onClick={() => { setSelectedCategory('Todos'); setSearchTerm(''); }}
                  className="mt-3 text-sm text-[#B85B56] hover:underline"
                >
                  Limpar filtros
                </button>
              </div>
            ) : (
              <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6">
                {filteredProducts.map(product => (
                  <div 
                    key={product.id} 
                    className="group bg-white rounded-2xl overflow-hidden border border-stone-100 shadow-sm hover:shadow-md transition-all duration-300 flex flex-col justify-between"
                  >
                    <div>
                      {/* Imagem do Produto */}
                      <div className="relative aspect-[4/5] overflow-hidden bg-stone-100">
                        <img 
                          src={product.image} 
                          alt={product.name}
                          className="w-full h-full object-cover group-hover:scale-105 transition-transform duration-500"
                        />
                        <button 
                          onClick={() => toggleFavorite(product.id)}
                          className="absolute top-3 right-3 p-2 rounded-full bg-white/80 backdrop-blur-xs text-stone-700 hover:text-rose-600 transition-colors"
                        >
                          <Heart className={`w-4 h-4 ${favorites.includes(product.id) ? 'fill-rose-500 text-rose-500' : ''}`} />
                        </button>
                        
                        <span className="absolute bottom-3 left-3 bg-stone-900/70 text-white text-[10px] uppercase font-semibold px-2.5 py-1 rounded-full backdrop-blur-xs">
                          {product.styleTag}
                        </span>
                      </div>

                      {/* Informações */}
                      <div className="p-4">
                        <div className="flex justify-between items-start gap-2 mb-1">
                          <h3 className="font-serif font-semibold text-stone-800 text-base leading-snug group-hover:text-[#B85B56] transition-colors">
                            {product.name}
                          </h3>
                        </div>
                        <p className="text-xs text-stone-500 line-clamp-2 mb-3">
                          {product.description}
                        </p>
                        <div className="flex items-center gap-1 mb-2">
                          <Star className="w-3.5 h-3.5 fill-amber-400 text-amber-400" />
                          <span className="text-xs font-medium text-stone-600">{product.rating}</span>
                        </div>
                      </div>
                    </div>

                    {/* Preço e Botão Adicionar */}
                    <div className="p-4 pt-0 flex items-center justify-between">
                      <div>
                        <span className="text-xs text-stone-400 font-light block">Preço</span>
                        <span className="text-lg font-bold text-stone-900">
                          R$ {product.price.toFixed(2).replace('.', ',')}
                        </span>
                      </div>
                      <button 
                        onClick={() => addToCart(product)}
                        className="bg-stone-900 hover:bg-[#B85B56] text-white p-2.5 rounded-xl transition-colors duration-200 shadow-xs"
                        title="Adicionar ao Carrinho"
                      >
                        <ShoppingBag className="w-4 h-4" />
                      </button>
                    </div>
                  </div>
                ))}
              </div>
            )}
          </div>
        )}

        {/* ================= ABA 2: QUIZ DE ESTILO ================= */}
        {activeTab === 'quiz' && (
          <div className="max-w-2xl mx-auto bg-white rounded-3xl p-6 sm:p-10 shadow-sm border border-stone-100">
            {!quizCompleted ? (
              <div>
                <div className="text-center mb-8">
                  <span className="inline-flex items-center gap-1 text-xs font-semibold uppercase tracking-widest text-[#B85B56] bg-rose-50 px-3 py-1 rounded-full">
                    <Sparkles className="w-3.5 h-3.5" /> Personal Stylist IA
                  </span>
                  <h2 className="font-serif text-2xl sm:text-3xl font-bold text-stone-900 mt-2">
                    Descubra Seu Estilo Dominante
                  </h2>
                  <p className="text-stone-500 text-sm mt-1">
                    Responda às perguntas abaixo para receber recomendações sob medida da nossa coleção.
                  </p>
                </div>

                {/* Perguntas */}
                <div className="space-y-8">
                  {QUIZ_QUESTIONS.map((q, idx) => (
                    <div key={q.id} className="border-b border-stone-100 pb-6">
                      <h3 className="font-medium text-stone-800 text-base mb-3">
                        {idx + 1}. {q.question}
                      </h3>
                      <div className="space-y-2.5">
                        {q.options.map((option, oIdx) => {
                          const isSelected = quizAnswers[q.id] === option.style;
                          return (
                            <button
                              key={oIdx}
                              onClick={() => handleAnswerSelect(q.id, option.style)}
                              className={`w-full text-left p-4 rounded-xl border text-sm transition-all flex items-center justify-between ${
                                isSelected
                                  ? 'border-[#B85B56] bg-rose-50/50 text-[#B85B56] font-medium shadow-xs'
                                  : 'border-stone-200 hover:border-stone-300 text-stone-700 bg-stone-50/50'
                              }`}
                            >
                              <span>{option.label}</span>
                              {isSelected && <CheckCircle2 className="w-4 h-4 text-[#B85B56]" />}
                            </button>
                          );
                        })}
                      </div>
                    </div>
                  ))}
                </div>

                {/* Ação Final do Quiz */}
                <div className="mt-8">
                  <button
                    onClick={calculateQuizResult}
                    disabled={Object.keys(quizAnswers).length < QUIZ_QUESTIONS.length}
                    className={`w-full py-3.5 rounded-xl font-medium text-white shadow-md transition-all flex items-center justify-center gap-2 ${
                      Object.keys(quizAnswers).length === QUIZ_QUESTIONS.length
                        ? 'bg-[#B85B56] hover:bg-[#a04e4a] cursor-pointer'
                        : 'bg-stone-300 cursor-not-allowed'
                    }`}
                  >
                    Ver Resultado e Recomendações
                    <ChevronRight className="w-4 h-4" />
                  </button>
                </div>
              </div>
            ) : (
              /* RESULTADO DO QUIZ */
              <div className="text-center py-6 space-y-6">
                <div className="inline-flex p-4 rounded-full bg-rose-100 text-[#B85B56]">
                  <Sparkles className="w-8 h-8" />
                </div>
                <div>
                  <span className="text-xs uppercase tracking-widest text-stone-400 font-semibold">Resultado do Seu Perfil</span>
                  <h2 className="font-serif text-3xl font-bold text-stone-900 mt-1">
                    Estilo {recommendedStyle}
                  </h2>
                  <p className="text-stone-600 text-sm mt-3 max-w-md mx-auto leading-relaxed">
                    {recommendedStyle === 'Elegante' && "Você valoriza sofisticação, cortes impecáveis e peças atemporais. Seu guarda-roupa ideal é refinado, clássico e marcante."}
                    {recommendedStyle === 'Romântico' && "Sua marca registrada é a delicadeza. Você aprecia fluidez, tecidos suaves, detalhes apaixonantes e toque artesanal."}
                    {recommendedStyle === 'Moderno' && "Você adora tendências contemporâneas, combinações marcantes e peças com personalidade forte para se destacar em qualquer ocasião."}
                  </p>
                </div>

                {/* Card de Peças Recomendadas */}
                <div className="bg-stone-50 rounded-2xl p-4 text-left border border-stone-200/60">
                  <h4 className="text-xs font-semibold text-stone-500 uppercase tracking-wider mb-3">
                    Sugestões Especiais para Você:
                  </h4>
                  <div className="grid grid-cols-2 gap-3">
                    {PRODUCTS.filter(p => p.styleTag === recommendedStyle).slice(0, 2).map(p => (
                      <div key={p.id} className="flex items-center gap-3 bg-white p-2.5 rounded-xl border border-stone-100">
                        <img src={p.image} alt={p.name} className="w-12 h-12 object-cover rounded-lg" />
                        <div>
                          <p className="text-xs font-medium text-stone-800 line-clamp-1">{p.name}</p>
                          <p className="text-xs font-bold text-[#B85B56]">R$ {p.price.toFixed(2)}</p>
                        </div>
                      </div>
                    ))}
                  </div>
                </div>

                <div className="flex flex-col sm:flex-row gap-3 pt-2">
                  <button 
                    onClick={() => setActiveTab('store')}
                    className="flex-1 bg-[#B85B56] text-white py-3 rounded-xl font-medium hover:bg-[#a04e4a] transition-colors"
                  >
                    Ver Catálogo Recomendado
                  </button>
                  <button 
                    onClick={resetQuiz}
                    className="flex items-center justify-center gap-2 border border-stone-200 text-stone-600 py-3 px-4 rounded-xl hover:bg-stone-50 font-medium"
                  >
                    <RefreshCw className="w-4 h-4" />
                    Refazer Quiz
                  </button>
                </div>
              </div>
            )}
          </div>
        )}

        {/* ================= ABA 3: CARRINHO DE COMPRAS ================= */}
        {activeTab === 'cart' && (
          <div className="max-w-4xl mx-auto">
            <h2 className="font-serif text-2xl sm:text-3xl font-bold text-stone-900 mb-6 flex items-center gap-3">
              <ShoppingBag className="w-7 h-7 text-[#B85B56]" />
              Seu Carrinho de Compras
            </h2>

            {cart.length === 0 ? (
              <div className="text-center py-16 bg-white rounded-3xl border border-stone-100 shadow-xs">
                <ShoppingBag className="w-12 h-12 text-stone-300 mx-auto mb-3" />
                <p className="text-stone-600 font-serif text-lg">Seu carrinho está vazio</p>
                <p className="text-stone-400 text-sm mt-1 mb-6">Explore o nosso catálogo e encontre os seus looks favoritos.</p>
                <button 
                  onClick={() => setActiveTab('store')}
                  className="bg-[#B85B56] text-white font-medium px-6 py-2.5 rounded-xl hover:bg-[#a04e4a] transition-colors"
                >
                  Explorar Coleção
                </button>
              </div>
            ) : (
              <div className="grid grid-cols-1 lg:grid-cols-3 gap-8">
                {/* Lista dos Itens */}
                <div className="lg:col-span-2 space-y-4">
                  {cart.map(item => (
                    <div 
                      key={item.id} 
                      className="bg-white rounded-2xl p-4 flex gap-4 items-center border border-stone-100 shadow-xs"
                    >
                      <img 
                        src={item.image} 
                        alt={item.name} 
                        className="w-20 h-24 object-cover rounded-xl bg-stone-100"
                      />
                      <div className="flex-1 min-w-0">
                        <h4 className="font-serif font-semibold text-stone-800 text-sm sm:text-base truncate">
                          {item.name}
                        </h4>
                        <p className="text-xs text-stone-400 mb-2">Estilo: {item.styleTag}</p>
                        <p className="text-sm font-bold text-stone-900">
                          R$ {item.price.toFixed(2).replace('.', ',')}
                        </p>
                      </div>

                      {/* Controles de Quantidade */}
                      <div className="flex items-center border border-stone-200 rounded-lg overflow-hidden">
                        <button 
                          onClick={() => updateQuantity(item.id, -1)}
                          className="p-1.5 hover:bg-stone-100 text-stone-600"
                        >
                          <Minus className="w-3.5 h-3.5" />
                        </button>
                        <span className="px-3 text-xs font-semibold text-stone-800">
                          {item.quantity}
                        </span>
                        <button 
                          onClick={() => updateQuantity(item.id, 1)}
                          className="p-1.5 hover:bg-stone-100 text-stone-600"
                        >
                          <Plus className="w-3.5 h-3.5" />
                        </button>
                      </div>

                      {/* Excluir */}
                      <button 
                        onClick={() => removeFromCart(item.id)}
                        className="text-stone-400 hover:text-rose-500 p-2"
                        title="Remover produto"
                      >
                        <Trash2 className="w-4 h-4" />
                      </button>
                    </div>
                  ))}

                  <button 
                    onClick={clearCart}
                    className="text-xs text-stone-500 hover:text-rose-600 font-medium flex items-center gap-1 pt-2"
                  >
                    <Trash2 className="w-3.5 h-3.5" /> Esvaziar carrinho
                  </button>
                </div>

                {/* Resumo da Compra */}
                <div className="bg-white rounded-2xl p-6 border border-stone-100 shadow-xs h-fit space-y-4">
                  <h3 className="font-serif font-bold text-stone-900 text-lg border-b border-stone-100 pb-3">
                    Resumo do Pedido
                  </h3>
                  
                  <div className="space-y-2 text-sm">
                    <div className="flex justify-between text-stone-600">
                      <span>Subtotal ({cartItemCount} itens)</span>
                      <span>R$ {cartTotal.toFixed(2).replace('.', ',')}</span>
                    </div>
                    <div className="flex justify-between text-stone-600">
                      <span>Frete</span>
                      <span className="text-emerald-600 font-medium">
                        {cartTotal >= 299 ? 'GRÁTIS' : 'R$ 19,90'}
                      </span>
                    </div>
                  </div>

                  <div className="border-t border-stone-100 pt-3 flex justify-between items-center">
                    <span className="font-bold text-stone-900 text-base">Total</span>
                    <span className="font-bold text-stone-900 text-xl">
                      R$ {(cartTotal >= 299 ? cartTotal : cartTotal + 19.90).toFixed(2).replace('.', ',')}
                    </span>
                  </div>

                  <button 
                    onClick={() => alert("Pedido realizado com sucesso! (Demonstração do aplicativo)")}
                    className="w-full bg-stone-900 hover:bg-[#B85B56] text-white font-medium py-3 rounded-xl transition-colors shadow-md mt-2"
                  >
                    Finalizar Compra
                  </button>

                  <div className="space-y-2 pt-4 border-t border-stone-100">
                    <div className="flex items-center gap-2 text-xs text-stone-500">
                      <ShieldCheck className="w-4 h-4 text-emerald-600" />
                      Pagamento 100% Seguro
                    </div>
                    <div className="flex items-center gap-2 text-xs text-stone-500">
                      <Truck className="w-4 h-4 text-stone-400" />
                      Envio Expresso para todo o Brasil
                    </div>
                    <div className="flex items-center gap-2 text-xs text-stone-500">
                      <RotateCcw className="w-4 h-4 text-stone-400" />
                      Primeira troca grátis em até 30 dias
                    </div>
                  </div>
                </div>
              </div>
            )}
          </div>
        )}
      </main>

      {/* RODAPÉ (FOOTER) */}
      <footer className="bg-stone-900 text-stone-300 pt-12 pb-8 border-t border-stone-800 mt-16">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 grid grid-cols-1 md:grid-cols-4 gap-8 mb-8">
          <div>
            <span className="font-serif text-2xl font-bold tracking-widest text-white">AURA</span>
            <p className="text-xs text-stone-400 mt-2 leading-relaxed">
              Moda feminina sofisticada e atemporal. Projetada para valorizar a essência de cada mulher.
            </p>
          </div>
          <div>
            <h5 className="text-xs font-semibold text-white uppercase tracking-wider mb-3">Links Rápidos</h5>
            <ul className="space-y-2 text-xs text-stone-400">
              <li><button onClick={() => setActiveTab('store')} className="hover:text-white">Coleção Atual</button></li>
              <li><button onClick={() => setActiveTab('quiz')} className="hover:text-white">Quiz de Estilo IA</button></li>
              <li><button onClick={() => setActiveTab('cart')} className="hover:text-white">Meu Carrinho</button></li>
            </ul>
          </div>
          <div>
            <h5 className="text-xs font-semibold text-white uppercase tracking-wider mb-3">Suporte & Dúvidas</h5>
            <ul className="space-y-2 text-xs text-stone-400">
              <li><a href="#" className="hover:text-white">Política de Entregas</a></li>
              <li><a href="#" className="hover:text-white">Trocas e Devoluções</a></li>
              <li><a href="#" className="hover:text-white">Guia de Tamanhos</a></li>
            </ul>
          </div>
          <div>
            <h5 className="text-xs font-semibold text-white uppercase tracking-wider mb-3">Projeto DSA 2026</h5>
            <p className="text-xs text-stone-400">
              Desenvolvimento de Aplicação ReactJS integrada com persistência local de dados (`useState` e `useEffect`).
            </p>
          </div>
        </div>
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 border-t border-stone-800 pt-6 text-center text-xs text-stone-500">
          © 2026 Aura Boutique - Todos os direitos reservados. Projeto Acadêmico DSA.
        </div>
      </footer>
    </div>
  );
}
