<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="Amber HoldCo">
    <title>Венчурний Дашборд - АМБЕР КАПІТАЛ (Platinum)</title>
    
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/react@18/umd/react.production.min.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js" crossorigin></script>
    <script src="https://unpkg.com/prop-types/prop-types.min.js"></script>
    <script src="https://unpkg.com/recharts@2.1.9/umd/Recharts.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

    <style>
        body { 
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; 
            -webkit-tap-highlight-color: transparent;
            background-color: #f1f5f9;
        }
        .scrollbar-hide::-webkit-scrollbar { display: none; }
        .scrollbar-hide { -ms-overflow-style: none; scrollbar-width: none; }
        .custom-scrollbar::-webkit-scrollbar { width: 6px; height: 6px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: #f8fafc; border-radius: 4px; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 4px; }
        .custom-scrollbar::-webkit-scrollbar-thumb:hover { background: #94a3b8; }
        
        .pb-safe { padding-bottom: env(safe-area-inset-bottom, 20px); }
        
        /* Modern Range slider styling */
        input[type=range] { -webkit-appearance: none; width: 100%; background: transparent; }
        input[type=range]:focus { outline: none; }
        input[type=range]::-webkit-slider-thumb { -webkit-appearance: none; height: 22px; width: 22px; border-radius: 50%; background: #ffffff; border: 3px solid #4f46e5; cursor: pointer; margin-top: -8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); transition: transform 0.1s; }
        input[type=range]::-webkit-slider-thumb:hover { transform: scale(1.15); }
        input[type=range]::-webkit-slider-runnable-track { width: 100%; height: 6px; cursor: pointer; background: #cbd5e1; border-radius: 3px; box-shadow: inset 0 1px 2px rgba(0,0,0,0.1); }
        
        /* Animated connection lines for structure */
        .glow-effect:hover { box-shadow: 0 0 20px rgba(59, 130, 246, 0.4); border-color: #60a5fa; }
        .glow-effect-fund:hover { box-shadow: 0 0 20px rgba(245, 158, 11, 0.4); border-color: #fbbf24; }
    </style>
</head>
<body class="text-slate-900 pb-20 md:pb-0">
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect, useMemo } = React;
        const { PieChart, Pie, Cell, AreaChart, Area, BarChart, Bar, XAxis, YAxis, CartesianGrid, Tooltip, ResponsiveContainer, Legend, ReferenceLine } = window.Recharts;

        // --- ICONS ---
        const Icon = ({ d, className }) => <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}>{d}</svg>;
        const PieChartIcon = (p) => <Icon {...p} d={<><path d="M21.21 15.89A10 10 0 1 1 8 2.83"></path><path d="M22 12A10 10 0 0 0 12 2v10z"></path></>} />;
        const Network = (p) => <Icon {...p} d={<><rect x="16" y="16" width="6" height="6" rx="1"/><rect x="2" y="16" width="6" height="6" rx="1"/><rect x="9" y="2" width="6" height="6" rx="1"/><path d="M5 16v-3a1 1 0 0 1 1-1h12a1 1 0 0 1 1 1v3"/><path d="M12 12V8"/></>} />;
        const DollarSign = (p) => <Icon {...p} d={<><line x1="12" y1="1" x2="12" y2="23"></line><path d="M17 5H9.5a3.5 3.5 0 0 0 0 7h5a3.5 3.5 0 0 1 0 7H6"></path></>} />;
        const TrendingUp = (p) => <Icon {...p} d={<><polyline points="23 6 13.5 15.5 8.5 10.5 1 18"></polyline><polyline points="17 6 23 6 23 12"></polyline></>} />;
        const Target = (p) => <Icon {...p} d={<><circle cx="12" cy="12" r="10"/><circle cx="12" cy="12" r="6"/><circle cx="12" cy="12" r="2"/></>} />;
        const Settings = (p) => <Icon {...p} d={<><circle cx="12" cy="12" r="3"/><path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1 0 2.83 2 2 0 0 1-2.83 0l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-2 2 2 2 0 0 1-2-2v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83 0 2 2 0 0 1 0-2.83l.06-.06a1.65 1.65 0 0 0 .33-1.82 1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1-2-2 2 2 0 0 1 2-2h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 0-2.83 2 2 0 0 1 2.83 0l.06.06a1.65 1.65 0 0 0 1.82.33H9a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 2-2 2 2 0 0 1 2 2v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 0 2 2 0 0 1 0 2.83l-.06.06a1.65 1.65 0 0 0-.33 1.82V9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 2 2 2 2 0 0 1-2 2h-.09a1.65 1.65 0 0 0-1.51 1z"/></>} />;
        const Briefcase = (p) => <Icon {...p} d={<><rect x="2" y="7" width="20" height="14" rx="2" ry="2"/><path d="M16 21V5a2 2 0 0 0-2-2h-4a2 2 0 0 0-2 2v16"/></>} />;
        const Activity = (p) => <Icon {...p} d={<polyline points="22 12 18 12 15 21 9 3 6 12 2 12"></polyline>} />;
        const Info = (p) => <Icon {...p} d={<><circle cx="12" cy="12" r="10"></circle><line x1="12" y1="16" x2="12" y2="12"></line><line x1="12" y1="8" x2="12.01" y2="8"></line></>} />;
        const Zap = (p) => <Icon {...p} d={<><polygon points="13 2 3 14 12 14 11 22 21 10 12 10 13 2"/></>} />;

        // --- MATH & LOGIC ---
        const initialShares = { 'ВЮ': 8, 'Олежка': 9, 'Дима': 2, 'Малімон': 2, 'Кисилишин': 2, 'Куйда': 5, 'ESOP': 10, 'Семчишин': 31, 'Авдієвський': 31 };
        const TARGET_VU = 28, TARGET_KYSYLYSHYN = 0.5, TARGET_MALIMON = 0.5, TARGET_KUYDA = 3;

        const calcFinalShare = (name, initial) => {
            if (name === 'ВЮ') return TARGET_VU;
            if (name === 'Кисилишин') return TARGET_KYSYLYSHYN;
            if (name === 'Малімон') return TARGET_MALIMON;
            if (name === 'Куйда') return TARGET_KUYDA;
            if (name === 'Семчишин' || name === 'Авдієвський') {
                const vuGrowth = TARGET_VU - initialShares['ВЮ']; 
                const minorityDrop = (initialShares['Кисилишин'] - TARGET_KYSYLYSHYN) + (initialShares['Малімон'] - TARGET_MALIMON) + (initialShares['Куйда'] - TARGET_KUYDA);
                return initial - ((vuGrowth - minorityDrop) / 2); 
            }
            return initial;
        };

        const PIE_COLORS = ['#3b82f6', '#10b981', '#f59e0b', '#6366f1', '#ec4899', '#8b5cf6', '#14b8a6', '#f97316', '#64748b'];

        const Card = ({ children, className = '' }) => <div className={`bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden ${className}`}>{children}</div>;
        const KPIWidget = ({ title, value, subtext, icon: Icon, colorClass = "text-slate-700 bg-slate-50" }) => (
          <Card className="p-4 md:p-6">
            <div className="flex justify-between items-start">
              <div><p className="text-[10px] md:text-xs font-bold text-slate-500 mb-1 uppercase tracking-wider">{title}</p><h3 className="text-xl md:text-2xl font-black text-slate-900 tracking-tight">{value}</h3>{subtext && <p className="text-[10px] text-slate-400 mt-1.5 font-medium">{subtext}</p>}</div>
              <div className={`p-3 rounded-xl ${colorClass}`}><Icon className="w-5 h-5 md:w-6 md:h-6" /></div>
            </div>
          </Card>
        );

        function App() {
          const [activeTab, setActiveTab] = useState('valuation');
          const [currency, setCurrency] = useState('USD');
          const [multiplier, setMultiplier] = useState(9.1);

          // Evolution state (Slider) - Starting at $4M
          const [evalCap, setEvalCap] = useState(4); // M

          // Unit Economics States (In KG)
          const [costMining, setCostMining] = useState(150); // max 300 $/kg
          const [costProcessing, setCostProcessing] = useState(200); // max 500 $/kg
          const [costLogistics, setCostLogistics] = useState(150); // max 500 $/kg
          const [priceFCA, setPriceFCA] = useState(3500); // max 5000 $/kg
          const [volume, setVolume] = useState(20000); // max 50000 kg

          const [selectedNode, setSelectedNode] = useState('kif');

          const rates = { USD: 1, EUR: 0.93, UAH: 39.5 };
          const symbols = { USD: '$', EUR: '€', UAH: '₴' };
          const fmt = (val, precision = 1) => {
              if (isNaN(val) || val === undefined) return `${symbols[currency]}0`;
              const converted = val * rates[currency];
              const suffix = currency === 'UAH' ? ' млн' : 'M';
              return `${symbols[currency]}${converted.toFixed(precision)}${suffix}`;
          };

          // --- CORE DYNAMIC CALCULATIONS ---
          const marginPerKg = priceFCA - costMining - costProcessing - costLogistics;
          const dynamicEBITDAM = (marginPerKg * volume) / 1000000;
          
          const EBITDA_2030 = dynamicEBITDAM;
          const DEBT = 2.85; 
          const totalValuation = Math.max(0, EBITDA_2030 * multiplier);
          const reservesValueM = (400000 * priceFCA) / 1000000;
          const netEquityValue = Math.max(0, totalValuation - DEBT);
          
          // Safe value for iOS PieChart to avoid crash on 0
          const safeVal = (v) => Math.max(0.1, v);

          const sotpData = [
              { name: 'Видобуток (Mining)', value: safeVal(totalValuation * 0.30), raw: totalValuation * 0.30 },
              { name: 'Фабрика (Processing)', value: safeVal(totalValuation * 0.50), raw: totalValuation * 0.50 },
              { name: 'Трейдинг (Logistics)', value: safeVal(totalValuation * 0.20), raw: totalValuation * 0.20 },
          ];

          const waterfallData = [
              { name: 'Enterprise Value', value: totalValuation, fill: '#3b82f6' },
              { name: 'Зобов\'язання (Buyout)', value: -DEBT, fill: '#ef4444' },
              { name: 'Net Equity Value', value: netEquityValue, fill: '#10b981' }
          ];

          const capTableData = Object.keys(initialShares).map((name, idx) => {
              const start = initialShares[name];
              const final = calcFinalShare(name, start);
              const exitVal = (final / 100) * netEquityValue;
              return { id: idx + 1, name, start, final, exitVal };
          }).sort((a, b) => b.start - a.start);

          // VU Evolution calculations (Interpolation 4M to 500M)
          const getVuShareAtCap = (cap) => {
              if (cap <= 4) return 8;
              if (cap >= 500) return 28;
              return 8 + ((cap - 4) / 496) * 20; // Linear interpolation
          };
          
          const currentVuShare = getVuShareAtCap(evalCap);
          const currentOthersShare = 100 - currentVuShare;
          
          const vuEvolData = [];
          const steps = [4, 25, 50, 75, 100, 150, 200, 300, 400, 500, 600, 700, 800];
          steps.forEach(c => {
              const vu = getVuShareAtCap(c);
              vuEvolData.push({ cap: c, vu: parseFloat(vu.toFixed(2)), others: parseFloat((100 - vu).toFixed(2)) });
          });

          const unitChartData = [
              { name: 'Економіка 1 кг', 'Видобуток': costMining, 'Збагачення': costProcessing, 'Логістика/Інше': costLogistics, 'Чиста Маржа': Math.max(0, marginPerKg) }
          ];

          const tabs = [
            { id: 'valuation', name: 'Оцінка & Exit', icon: Target },
            { id: 'captable', name: 'Cap Table', icon: PieChartIcon },
            { id: 'finance', name: 'Юніт & Фінанси', icon: Activity },
            { id: 'structure', name: 'Архітектура', icon: Network },
            { id: 'vu_evolution', name: 'Зростання долі ВЮ', icon: TrendingUp },
          ];

          // Hardcoded structure styles to bypass Tailwind JIT compiler issues
          const structureStyles = {
              amber: { bgSel: 'bg-amber-50 border-amber-500 ring-2 ring-amber-200', text: 'text-amber-700', sub: 'text-amber-500' },
              blue: { bgSel: 'bg-blue-50 border-blue-500 ring-2 ring-blue-200', text: 'text-blue-700', sub: 'text-blue-500' },
              emerald: { bgSel: 'bg-emerald-50 border-emerald-500 ring-2 ring-emerald-200', text: 'text-emerald-700', sub: 'text-emerald-500' },
              purple: { bgSel: 'bg-purple-50 border-purple-500 ring-2 ring-purple-200', text: 'text-purple-700', sub: 'text-purple-500' }
          };

          const nodeDetails = {
              'ubo': { title: 'Власники (UBO)', type: 'Бенефіціари', desc: 'Кінцеві контролери групи через корпоративні права.', kpi1: '100%', kpi1L: 'Контроль', kpi2: 'Рада Директорів', kpi2L: 'Управління' },
              'kif': { title: 'КІФ «АМБЕР КАПІТАЛ»', type: 'Інвестиційний Фонд', desc: 'Забезпечує податкову оптимізацію та консолідацію дивідендів перед реінвестуванням.', kpi1: '0%', kpi1L: 'Податок на прибуток', kpi2: 'Акції', kpi2L: 'Форма капіталу' },
              'holdco': { title: 'ТОВ «Амбер Холдинг»', type: 'Керуюча компанія', desc: 'Операційний центр групи. Утримує ТМ, персонал та координує інші компанії.', kpi1: 'Management', kpi1L: 'Функція', kpi2: 'Royalty', kpi2L: 'Потоки' },
              'opco1': { title: '⛏️ OpCo (Видобуток)', type: 'Операційна компанія', desc: 'Тримачі спеціальних дозволів на користування надрами. Центр накопичення сировини.', kpi1: `$${costMining}/кг`, kpi1L: 'Собівартість', kpi2: `${(volume/1000).toFixed(1)}т`, kpi2L: 'Обсяг' },
              'fab': { title: '🏭 Фабрика', type: 'Переробка', desc: 'Центр створення доданої вартості. Збагачення руди до товарних кондицій.', kpi1: `$${costProcessing}/кг`, kpi1L: 'Процесинг', kpi2: 'CAPEX', kpi2L: 'Інтенсивність' },
              'tech': { title: '🚜 Амбер Технік', type: 'Логістика та Техніка', desc: 'Консолідує всю техніку та автопарк. Здає в оренду іншим компаніям групи.', kpi1: 'Оренда', kpi1L: 'Модель', kpi2: 'Внутрішній', kpi2L: 'Клієнт' },
              'trade': { title: '🚢 Амбер Трейд', type: 'Торговий Дім', desc: 'Єдине вікно експорту. Генерує валютну виручку, застосовує 0% ставку ПДВ.', kpi1: '0%', kpi1L: 'ПДВ (Експорт)', kpi2: `$${priceFCA}/кг`, kpi2L: 'FCA Ціна' },
              'charity': { title: '🤝 БФ «Амбер»', type: 'Благодійність', desc: 'Системна інтеграція КСВ у бізнес-модель. Фінансується відсотком від прибутку.', kpi1: 'до 4%', kpi1L: 'Від прибутку', kpi2: 'CSR', kpi2L: 'Місія' }
          };

          return (
            <div className="min-h-screen flex flex-col font-sans">
              
              {/* Header */}
              <header className="bg-slate-900 text-white px-4 md:px-8 py-3 sticky top-0 z-50 shadow-md">
                <div className="max-w-7xl mx-auto flex flex-col md:flex-row justify-between items-center gap-3">
                  <div className="flex items-center space-x-3 w-full md:w-auto justify-between">
                    <div>
                        <h1 className="text-xl font-bold tracking-tight text-transparent bg-clip-text bg-gradient-to-r from-blue-400 to-emerald-400">AMBER Holding</h1>
                        <p className="text-slate-400 text-[10px] uppercase tracking-widest font-semibold mt-0.5">КІФ "АМБЕР КАПІТАЛ"</p>
                    </div>
                    <div className="flex md:hidden bg-slate-800 rounded-lg p-1 border border-slate-700">
                        {['USD', 'EUR', 'UAH'].map(c => (
                            <button key={c} onClick={() => setCurrency(c)} className={`text-[10px] px-3 py-1.5 rounded font-bold transition-all ${currency === c ? 'bg-indigo-500 text-white shadow-sm' : 'text-slate-400'}`}>{c}</button>
                        ))}
                    </div>
                  </div>
                  
                  <div className="hidden md:flex items-center space-x-4">
                    <div className="flex bg-slate-800 rounded-lg p-1 border border-slate-700">
                        {['USD', 'EUR', 'UAH'].map(c => (
                            <button key={c} onClick={() => setCurrency(c)} className={`text-xs px-4 py-1.5 rounded-md font-bold transition-all ${currency === c ? 'bg-indigo-500 text-white shadow-sm' : 'text-slate-400 hover:text-white'}`}>{c}</button>
                        ))}
                    </div>
                    <div className="flex space-x-1 bg-slate-800/50 p-1 rounded-lg">
                      {tabs.map(tab => (
                        <button key={tab.id} onClick={() => setActiveTab(tab.id)} className={`flex items-center px-3 py-1.5 rounded-md text-xs font-semibold transition-all ${activeTab === tab.id ? 'bg-white text-slate-900 shadow-sm' : 'text-slate-300 hover:text-white'}`}>
                          <tab.icon className={`w-4 h-4 mr-1.5 ${activeTab === tab.id ? 'text-indigo-600' : ''}`} /> {tab.name}
                        </button>
                      ))}
                    </div>
                  </div>
                </div>
              </header>

              {/* Mobile Nav */}
              <div className="md:hidden fixed bottom-0 left-0 right-0 bg-white border-t border-slate-200 z-50 flex overflow-x-auto scrollbar-hide pb-safe shadow-[0_-4px_10px_rgba(0,0,0,0.08)]">
                  {tabs.map(tab => (
                      <button key={tab.id} onClick={() => setActiveTab(tab.id)} className={`flex flex-col items-center justify-center min-w-[75px] flex-shrink-0 py-2.5 px-1 transition-all ${activeTab === tab.id ? 'text-indigo-600 bg-indigo-50/50 border-t-2 border-indigo-600' : 'text-slate-500 border-t-2 border-transparent'}`}>
                          <tab.icon className={`w-5 h-5 mb-1 ${activeTab === tab.id ? 'animate-bounce' : ''}`} />
                          <span className="text-[10px] font-bold tracking-tight">{tab.name}</span>
                      </button>
                  ))}
              </div>

              {/* Main Content */}
              <main className="flex-grow max-w-7xl mx-auto w-full px-4 md:px-8 py-6 pb-28 md:pb-8">
                
                {/* Global KPIs */}
                <div className="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
                  <KPIWidget title="Enterprise Value" value={fmt(totalValuation)} subtext={`${multiplier}x EV/EBITDA`} icon={Target} colorClass="bg-blue-100 text-blue-700 shadow-inner" />
                  <KPIWidget title="Net Equity Value" value={fmt(netEquityValue)} subtext="Очищено від боргу" icon={DollarSign} colorClass="bg-emerald-100 text-emerald-700 shadow-inner" />
                  <KPIWidget title="Бюджет викупу" value={fmt(DEBT)} subtext="Викуп міноритаріїв (Treasury)" icon={Briefcase} colorClass="bg-amber-100 text-amber-700 shadow-inner" />
                </div>

                {/* --- TAB: VALUATION & EXIT --- */}
                {activeTab === 'valuation' && (
                  <div className="space-y-6 animate-fade-in">
                      <Card className="p-6 bg-white border-t-4 border-t-blue-500 shadow-md">
                          <h3 className="text-lg font-black flex items-center mb-6 text-slate-800"><Settings className="w-5 h-5 mr-2 text-blue-500"/> Калькулятор мультиплікаторів (EV/EBITDA)</h3>
                          <div className="flex flex-col md:flex-row items-center gap-6">
                              <div className="w-full md:w-3/5">
                                  <div className="flex justify-between mb-3 text-xs font-bold text-slate-500 uppercase">
                                      <span>Песимістично (5.0x)</span>
                                      <span className="text-blue-600 text-lg">{multiplier}x EBITDA</span>
                                      <span>Оптимістично (15.0x)</span>
                                  </div>
                                  <input type="range" min="5" max="15" step="0.1" value={multiplier} onChange={(e) => setMultiplier(parseFloat(e.target.value))} />
                                  <p className="text-[10px] text-slate-400 mt-3 text-center font-bold uppercase tracking-wider">
                                      EBITDA ({fmt(EBITDA_2030, 2)}) динамічно розраховується з Юніт Економіки
                                  </p>
                              </div>
                              <div className="w-full md:w-2/5 flex flex-col gap-3">
                                  <div className="text-center bg-slate-50 rounded-2xl p-4 border border-slate-200">
                                      <p className="text-[10px] text-slate-500 uppercase tracking-widest mb-1 font-bold">Оцінка Бізнесу (EV)</p>
                                      <p className="text-3xl font-black text-blue-600">{fmt(totalValuation, 0)}</p>
                                  </div>
                                  <div className="text-center bg-amber-50 rounded-2xl p-4 border border-amber-200 shadow-inner">
                                      <p className="text-[10px] text-amber-600 uppercase tracking-widest mb-1 font-bold">Вартість запасів (400т × {fmt(priceFCA, 0)}/кг)</p>
                                      <p className="text-2xl font-black text-amber-700">{fmt(reservesValueM, 1)}</p>
                                  </div>
                              </div>
                          </div>
                      </Card>

                      <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
                          <Card className="p-6 shadow-sm min-w-0">
                              <h3 className="text-sm font-bold text-slate-800 mb-4 border-b pb-2 uppercase tracking-wider">Sum-of-the-Parts (SOTP)</h3>
                              <div className="h-64">
                                  <ResponsiveContainer width="100%" height="100%">
                                      <PieChart>
                                          <Pie data={sotpData} cx="50%" cy="50%" innerRadius={60} outerRadius={80} paddingAngle={4} dataKey="value">
                                              {sotpData.map((e, i) => <Cell key={`cell-${i}`} fill={['#f59e0b', '#3b82f6', '#10b981'][i]} />)}
                                          </Pie>
                                          <Tooltip formatter={(value, name, props) => fmt(props.payload.raw)} contentStyle={{borderRadius:'8px', border:'none', boxShadow:'0 4px 6px -1px rgb(0 0 0 / 0.1)', fontWeight:'bold'}}/>
                                          <Legend verticalAlign="bottom" height={36} formatter={(value, entry) => <span className="text-xs font-bold text-slate-600">{value} ({fmt(entry.payload.raw)})</span>} />
                                      </PieChart>
                                  </ResponsiveContainer>
                              </div>
                          </Card>
                          <Card className="p-6 shadow-sm min-w-0">
                              <h3 className="text-sm font-bold text-slate-800 mb-4 border-b pb-2 uppercase tracking-wider">Каскад розподілу (Exit Waterfall)</h3>
                              <div className="h-64 mt-4">
                                  <ResponsiveContainer width="100%" height="100%">
                                      <BarChart data={waterfallData} margin={{ top: 20, right: 20, left: 0, bottom: 0 }}>
                                          <CartesianGrid strokeDasharray="3 3" vertical={false} stroke="#f1f5f9" />
                                          <XAxis dataKey="name" tick={{fontSize: 10, fontWeight: 'bold'}} interval={0} />
                                          <Tooltip cursor={{fill: 'transparent'}} formatter={(val) => fmt(val)} contentStyle={{borderRadius:'8px', fontWeight:'bold'}}/>
                                          <Bar dataKey="value" radius={[6,6,0,0]} barSize={50}>
                                              {waterfallData.map((entry, index) => <Cell key={`cell-${index}`} fill={entry.fill} />)}
                                          </Bar>
                                      </BarChart>
                                  </ResponsiveContainer>
                              </div>
                          </Card>
                      </div>
                  </div>
                )}

                {/* --- TAB: CAP TABLE --- */}
                {activeTab === 'captable' && (
                    <Card className="p-6 overflow-hidden animate-fade-in">
                        <div className="overflow-x-auto custom-scrollbar pb-4">
                            <table className="w-full text-left border-collapse min-w-[650px]">
                              <thead>
                                <tr className="bg-slate-100 text-slate-600 text-[10px] md:text-xs uppercase tracking-wider">
                                  <th className="p-3 font-semibold border-b">Учасник</th>
                                  <th className="p-3 font-semibold border-b text-center">Старт</th>
                                  <th className="p-3 font-semibold border-b text-center bg-blue-50">Exit %</th>
                                  <th className="p-3 font-semibold border-b text-right bg-emerald-50">Exit Value ($M)</th>
                                </tr>
                              </thead>
                              <tbody className="text-sm divide-y divide-slate-100">
                                {capTableData.map((row) => (
                                  <tr key={row.id} className="hover:bg-slate-50 transition-colors">
                                    <td className="p-3 font-black text-slate-900">{row.name}</td>
                                    <td className="p-3 text-center text-slate-500 font-medium">{row.start.toFixed(1)}%</td>
                                    <td className="p-3 text-center font-bold text-blue-700 bg-blue-50/30">{row.final.toFixed(1)}%</td>
                                    <td className="p-3 text-right font-bold text-emerald-700 bg-emerald-50/30">{fmt(row.exitVal, 2)}</td>
                                  </tr>
                                ))}
                              </tbody>
                            </table>
                        </div>
                    </Card>
                )}

                {/* --- TAB: UNIT ECONOMICS & FINANCE --- */}
                {activeTab === 'finance' && (
                  <div className="space-y-6 animate-fade-in">
                      <Card className="p-6 bg-white border-t-4 border-t-emerald-500 shadow-md">
                          <div className="flex justify-between items-start mb-6">
                              <div>
                                  <h3 className="text-lg font-black text-slate-800 flex items-center">
                                      <Zap className="w-5 h-5 mr-2 text-emerald-500"/> Unit Economics (Калькулятор)
                                  </h3>
                                  <p className="text-xs text-slate-500 mt-1 font-medium">Моделювання в кілограмах. Впливає на загальну оцінку Холдингу.</p>
                              </div>
                              <div className="bg-emerald-50 border border-emerald-200 px-4 py-2 rounded-xl text-center">
                                  <p className="text-[10px] uppercase font-bold text-emerald-600">Total EBITDA (Рік)</p>
                                  <p className="text-xl font-black text-emerald-700">{fmt(dynamicEBITDAM, 2)}</p>
                              </div>
                          </div>

                          <div className="grid grid-cols-1 lg:grid-cols-2 gap-8">
                              <div className="space-y-6">
                                  {/* FCA Price Slider */}
                                  <div>
                                      <div className="flex justify-between mb-1"><span className="text-xs font-bold text-slate-700">Експортна Ціна (FCA)</span><span className="text-sm font-black text-indigo-600">${priceFCA}/кг</span></div>
                                      <input type="range" min="500" max="5000" step="50" value={priceFCA} onChange={e=>setPriceFCA(Number(e.target.value))} />
                                  </div>
                                  {/* Mining Cost */}
                                  <div>
                                      <div className="flex justify-between mb-1"><span className="text-xs font-bold text-slate-700">Собівартість: Видобуток</span><span className="text-sm font-black text-amber-600">${costMining}/кг</span></div>
                                      <input type="range" min="10" max="300" step="5" value={costMining} onChange={e=>setCostMining(Number(e.target.value))} />
                                  </div>
                                  {/* Processing Cost */}
                                  <div>
                                      <div className="flex justify-between mb-1"><span className="text-xs font-bold text-slate-700">Собівартість: Збагачення</span><span className="text-sm font-black text-blue-600">${costProcessing}/кг</span></div>
                                      <input type="range" min="10" max="500" step="10" value={costProcessing} onChange={e=>setCostProcessing(Number(e.target.value))} />
                                  </div>
                                  {/* Logistics Cost */}
                                  <div>
                                      <div className="flex justify-between mb-1"><span className="text-xs font-bold text-slate-700">Логістика та Інше</span><span className="text-sm font-black text-slate-600">${costLogistics}/кг</span></div>
                                      <input type="range" min="10" max="500" step="10" value={costLogistics} onChange={e=>setCostLogistics(Number(e.target.value))} />
                                  </div>
                                  <hr className="border-slate-200" />
                                  {/* Volume Slider */}
                                  <div>
                                      <div className="flex justify-between mb-1"><span className="text-xs font-bold text-emerald-700">Річний Обсяг (кілограми)</span><span className="text-sm font-black text-emerald-600">{volume.toLocaleString()} кг</span></div>
                                      <input type="range" min="1000" max="50000" step="500" value={volume} onChange={e=>setVolume(Number(e.target.value))} />
                                  </div>
                              </div>
                              
                              <div className="h-full min-h-[300px]">
                                  <ResponsiveContainer width="100%" height="100%">
                                      <BarChart data={unitChartData} layout="vertical" margin={{ top: 20, right: 30, left: 20, bottom: 5 }}>
                                          <CartesianGrid strokeDasharray="3 3" horizontal={true} vertical={false} />
                                          <XAxis type="number" hide />
                                          <YAxis dataKey="name" type="category" hide />
                                          <Tooltip cursor={{fill: 'transparent'}} contentStyle={{borderRadius:'8px', fontWeight:'bold'}} formatter={(v)=>`$${v.toFixed(1)}/кг`} />
                                          <Legend wrapperStyle={{paddingTop: '10px'}} />
                                          <Bar dataKey="Видобуток" stackId="a" fill="#f59e0b" barSize={60} />
                                          <Bar dataKey="Збагачення" stackId="a" fill="#3b82f6" />
                                          <Bar dataKey="Логістика/Інше" stackId="a" fill="#94a3b8" />
                                          <Bar dataKey="Чиста Маржа" stackId="a" fill="#10b981" radius={[0, 8, 8, 0]} />
                                      </BarChart>
                                  </ResponsiveContainer>
                              </div>
                          </div>
                      </Card>
                  </div>
                )}

                {/* --- TAB: STRUCTURE 2.0 (INTERACTIVE) --- */}
                {activeTab === 'structure' && (
                  <div className="animate-fade-in flex flex-col h-full">
                      <div className="bg-slate-900 text-white p-5 rounded-t-xl flex justify-between items-center">
                          <div>
                              <h2 className="text-lg font-black tracking-wide flex items-center"><Network className="w-5 h-5 mr-2 text-indigo-400"/> Інтерактивна Архітектура Холдингу</h2>
                              <p className="text-xs text-slate-400 font-medium mt-1">Клікніть на будь-який елемент для підсвічування ланцюгів зв'язку.</p>
                          </div>
                      </div>
                      
                      <div className="flex flex-col lg:flex-row bg-white border border-t-0 border-slate-200 rounded-b-xl shadow-sm overflow-hidden min-h-[600px]">
                          
                          {/* LEFT: Diagram Area */}
                          <div className="w-full lg:w-2/3 p-6 md:p-10 bg-slate-50 relative overflow-x-auto custom-scrollbar flex flex-col items-center justify-start min-w-[600px] border-r border-slate-200">
                              
                              <button onClick={()=>setSelectedNode('ubo')} className={`relative z-10 w-64 p-4 rounded-xl border-2 transition-all duration-300 transform glow-effect ${selectedNode==='ubo' ? 'bg-slate-800 text-white border-slate-800 scale-105 shadow-xl' : 'bg-white text-slate-800 border-slate-300 shadow-md'}`}>
                                  <div className="text-[10px] uppercase font-bold tracking-widest opacity-70 mb-1">Кінцеві Бенефіціари</div>
                                  <div className="font-black text-lg">👤 Власники (UBO)</div>
                              </button>
                              
                              <div className={`w-1 mx-auto transition-all duration-500 h-8 ${['ubo','kif'].includes(selectedNode) ? 'bg-indigo-500 shadow-[0_0_12px_#6366f1] animate-pulse' : 'bg-slate-300'}`}></div>
                              
                              <button onClick={()=>setSelectedNode('kif')} className={`relative z-10 w-80 p-5 rounded-xl border-2 transition-all duration-300 transform glow-effect-fund ${selectedNode==='kif' ? 'bg-amber-500 text-white border-amber-600 scale-105 shadow-[0_10px_25px_rgba(245,158,11,0.5)]' : 'bg-gradient-to-r from-amber-50 to-amber-100 text-amber-900 border-amber-300 shadow-md'}`}>
                                  <div className="text-[10px] uppercase font-bold tracking-widest opacity-80 mb-1">Податковий Щит (0%)</div>
                                  <div className="font-black text-xl">🏦 КІФ «АМБЕР КАПІТАЛ»</div>
                              </button>

                              <div className={`w-1 mx-auto transition-all duration-500 h-8 ${['kif','holdco'].includes(selectedNode) ? 'bg-indigo-500 shadow-[0_0_12px_#6366f1] animate-pulse' : 'bg-slate-300'}`}></div>

                              <button onClick={()=>setSelectedNode('holdco')} className={`relative z-10 w-72 p-4 rounded-xl border-2 transition-all duration-300 transform glow-effect ${selectedNode==='holdco' ? 'bg-slate-700 text-white border-slate-800 scale-105 shadow-xl' : 'bg-white text-slate-800 border-slate-400 shadow-md'}`}>
                                  <div className="text-[10px] uppercase font-bold tracking-widest opacity-70 mb-1">Керуюча Компанія & ТМ</div>
                                  <div className="font-black text-lg">🏢 ТОВ «Амбер Холдинг»</div>
                              </button>

                              <div className="relative w-full max-w-[800px] h-8 mt-0">
                                  <div className={`absolute top-0 left-1/2 -translate-x-1/2 w-1 h-full transition-all duration-500 ${selectedNode === 'holdco' || ['opco1','fab','tech','trade'].includes(selectedNode) ? 'bg-indigo-500 shadow-[0_0_8px_#6366f1] animate-pulse' : 'bg-slate-300'}`}></div>
                                  <div className={`absolute top-1/2 left-[12.5%] right-[12.5%] h-1 transition-all duration-500 ${selectedNode === 'holdco' || ['opco1','fab','tech','trade'].includes(selectedNode) ? 'bg-indigo-500 shadow-[0_0_8px_#6366f1] animate-pulse' : 'bg-slate-300'}`}></div>
                                  
                                  <div className={`absolute top-1/2 left-[12.5%] w-1 h-1/2 transition-all duration-500 ${selectedNode === 'holdco' || selectedNode === 'opco1' ? 'bg-indigo-500 shadow-[0_0_8px_#6366f1] animate-pulse' : 'bg-slate-300'}`}></div>
                                  <div className={`absolute top-1/2 left-[37.5%] w-1 h-1/2 transition-all duration-500 ${selectedNode === 'holdco' || selectedNode === 'fab' ? 'bg-indigo-500 shadow-[0_0_8px_#6366f1] animate-pulse' : 'bg-slate-300'}`}></div>
                                  <div className={`absolute top-1/2 left-[62.5%] w-1 h-1/2 transition-all duration-500 ${selectedNode === 'holdco' || selectedNode === 'tech' ? 'bg-indigo-500 shadow-[0_0_8px_#6366f1] animate-pulse' : 'bg-slate-300'}`}></div>
                                  <div className={`absolute top-1/2 left-[87.5%] w-1 h-1/2 transition-all duration-500 ${selectedNode === 'holdco' || selectedNode === 'trade' ? 'bg-indigo-500 shadow-[0_0_8px_#6366f1] animate-pulse' : 'bg-slate-300'}`}></div>
                              </div>

                              <div className="flex w-full max-w-[800px] justify-between relative z-10 px-4">
                                  {['opco1', 'fab', 'tech', 'trade'].map((id, i) => {
                                      const titles = ['⛏️ OpCo 1-3', '🏭 Фабрика', '🚜 Амбер Технік', '🚢 Амбер Трейд'];
                                      const subs = ['Видобуток', 'Переробка', 'Логістика', 'Експорт (0% ПДВ)'];
                                      const colors = ['amber', 'blue', 'emerald', 'purple'];
                                      const c = colors[i];
                                      const isSel = selectedNode === id;
                                      
                                      return (
                                          <button key={id} onClick={()=>setSelectedNode(id)} className={`w-[22%] p-3 rounded-xl border-2 transition-all duration-300 transform shadow-md flex flex-col items-center text-center ${isSel ? `${structureStyles[c].bgSel} scale-110 z-20` : `bg-white border-slate-200 hover:scale-105`}`}>
                                              <div className={`font-black text-sm md:text-base ${isSel ? structureStyles[c].text : 'text-slate-800'}`}>{titles[i]}</div>
                                              <div className={`text-[9px] md:text-[10px] mt-1 font-bold uppercase ${isSel ? structureStyles[c].sub : 'text-slate-500'}`}>{subs[i]}</div>
                                          </button>
                                      )
                                  })}
                              </div>

                              <div className="w-full max-w-[800px] relative h-16 mt-6">
                                  <div className={`absolute top-0 left-1/2 -translate-x-1/2 w-1 h-full border-l-4 border-dashed transition-all duration-500 ${selectedNode === 'charity' || ['opco1','fab','tech','trade'].includes(selectedNode) ? 'border-emerald-500 animate-pulse' : 'border-slate-300'}`}></div>
                                  <div className={`absolute top-1/2 left-1/2 -translate-x-1/2 px-3 py-1 text-[10px] font-bold uppercase rounded-full transition-all duration-500 z-10 ${selectedNode === 'charity' ? 'bg-emerald-500 text-white shadow-[0_0_12px_#10b981] scale-110' : 'bg-white text-emerald-600 border border-emerald-200'}`}>до 4% прибутку</div>
                              </div>

                              <button onClick={()=>setSelectedNode('charity')} className={`relative z-10 w-64 p-3 rounded-full border-2 transition-all duration-300 transform glow-effect ${selectedNode==='charity' ? 'bg-emerald-600 text-white border-emerald-700 scale-110 shadow-[0_10px_25px_rgba(16,185,129,0.5)]' : 'bg-emerald-50 text-emerald-800 border-emerald-300 shadow-md'}`}>
                                  <div className="font-black text-base">🤝 БФ «Амбер»</div>
                              </button>

                          </div>

                          {/* RIGHT: Detail Panel */}
                          <div className="w-full lg:w-1/3 bg-white p-6 md:p-8 flex flex-col justify-center border-l border-slate-100">
                              {selectedNode && nodeDetails[selectedNode] ? (
                                  <div className="animate-fade-in">
                                      <div className="inline-block px-3 py-1 rounded-full bg-slate-100 text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-3">
                                          {nodeDetails[selectedNode].type}
                                      </div>
                                      <h3 className="text-2xl font-black text-slate-900 mb-4 leading-tight">{nodeDetails[selectedNode].title}</h3>
                                      <p className="text-sm text-slate-600 font-medium leading-relaxed mb-8">
                                          {nodeDetails[selectedNode].desc}
                                      </p>
                                      
                                      <div className="grid grid-cols-2 gap-4">
                                          <div className="bg-slate-50 rounded-xl p-4 border border-slate-100 shadow-sm">
                                              <p className="text-[10px] text-slate-400 font-bold uppercase mb-1">{nodeDetails[selectedNode].kpi1L}</p>
                                              <p className="text-xl font-black text-indigo-600">{nodeDetails[selectedNode].kpi1}</p>
                                          </div>
                                          <div className="bg-slate-50 rounded-xl p-4 border border-slate-100 shadow-sm">
                                              <p className="text-[10px] text-slate-400 font-bold uppercase mb-1">{nodeDetails[selectedNode].kpi2L}</p>
                                              <p className="text-xl font-black text-emerald-600">{nodeDetails[selectedNode].kpi2}</p>
                                          </div>
                                      </div>
                                  </div>
                              ) : null}
                          </div>
                      </div>
                  </div>
                )}

                {/* --- TAB: VU EVOLUTION --- */}
                {activeTab === 'vu_evolution' && (
                  <div className="space-y-6 animate-fade-in">
                    <Card className="p-6 md:p-8 bg-gradient-to-br from-slate-900 to-indigo-950 text-white border-none shadow-xl relative overflow-hidden">
                        <div className="absolute -top-20 -right-20 w-64 h-64 bg-indigo-500 rounded-full mix-blend-multiply filter blur-3xl opacity-50"></div>
                        <div className="absolute -bottom-20 -left-20 w-64 h-64 bg-emerald-500 rounded-full mix-blend-multiply filter blur-3xl opacity-50"></div>
                        
                        <div className="relative z-10 flex flex-col md:flex-row justify-between items-center gap-8">
                            <div className="w-full md:w-1/2">
                                <h2 className="text-2xl font-black mb-2 flex items-center"><TrendingUp className="w-6 h-6 mr-3 text-indigo-400"/> Зростання долі ВЮ</h2>
                                <p className="text-sm text-indigo-200 mb-6 font-medium leading-relaxed">Динамічне зростання частки партнера ВЮ від 8% до 28% пропорційно зростанню капіталізації холдингу (від $4M до $500M+).</p>
                                
                                <label className="text-[10px] font-bold text-indigo-300 uppercase tracking-widest block mb-3">Поточна Капіталізація (Слайдер)</label>
                                <input type="range" min="4" max="800" step="2" value={evalCap} onChange={(e) => setEvalCap(Number(e.target.value))} className="w-full" />
                                <div className="flex justify-between mt-2 text-[10px] md:text-xs font-bold text-indigo-400">
                                    <span>$4M</span><span>$500M (Target)</span><span>$800M</span>
                                </div>
                            </div>
                            
                            <div className="w-full md:w-1/2 flex gap-4">
                                <div className="flex-1 bg-white/10 backdrop-blur-md rounded-2xl p-5 border border-white/20 relative overflow-hidden">
                                    <div className="absolute top-0 left-0 w-1 h-full bg-indigo-400"></div>
                                    <p className="text-[10px] uppercase font-bold text-indigo-200 tracking-wider">Частка ВЮ</p>
                                    <p className="text-4xl font-black text-white mt-1">{currentVuShare.toFixed(1)}%</p>
                                    <p className="text-xs text-indigo-300 mt-2 font-medium">Вартість: {fmt((currentVuShare/100)*evalCap)}</p>
                                </div>
                                <div className="flex-1 bg-white/5 backdrop-blur-md rounded-2xl p-5 border border-white/10 relative overflow-hidden">
                                    <div className="absolute top-0 left-0 w-1 h-full bg-slate-400"></div>
                                    <p className="text-[10px] uppercase font-bold text-slate-300 tracking-wider">Пул Інших</p>
                                    <p className="text-4xl font-black text-slate-100 mt-1">{currentOthersShare.toFixed(1)}%</p>
                                    <p className="text-xs text-slate-400 mt-2 font-medium">Вартість: {fmt((currentOthersShare/100)*evalCap)}</p>
                                </div>
                            </div>
                        </div>
                    </Card>

                    <Card className="p-4 md:p-6 shadow-md border-t-4 border-t-indigo-500 min-w-0">
                        <h3 className="text-sm font-bold text-slate-800 mb-6 uppercase tracking-wider flex items-center">
                            <PieChartIcon className="w-5 h-5 mr-2 text-indigo-500"/> Перерозподіл ($M)
                        </h3>
                        <div className="h-[350px] w-full relative">
                            <ResponsiveContainer width="100%" height="100%">
                                <AreaChart data={vuEvolData} margin={{ top: 10, right: 5, left: -20, bottom: 0 }}>
                                    <CartesianGrid strokeDasharray="3 3" vertical={false} stroke="#e2e8f0" />
                                    <XAxis dataKey="cap" tick={{fontSize: 12, fontWeight: 600}} tickFormatter={(v)=>`$${v}M`} stroke="#94a3b8" />
                                    <YAxis tick={{fontSize: 12}} tickFormatter={(v)=>`${v}%`} stroke="#94a3b8" />
                                    <Tooltip 
                                        contentStyle={{borderRadius:'12px', border:'none', boxShadow:'0 10px 15px -3px rgb(0 0 0 / 0.1)', fontSize:'12px'}}
                                        formatter={(val) => [`${val}%`, '']}
                                        labelFormatter={(l)=>`Капіталізація: $${l}M`}
                                    />
                                    <Area type="monotone" dataKey="others" stackId="1" name="Інші Партнери" stroke="#94a3b8" fill="#cbd5e1" strokeWidth={2} />
                                    <Area type="monotone" dataKey="vu" stackId="1" name="Частка ВЮ" stroke="#4f46e5" fill="#818cf8" strokeWidth={3} activeDot={{r: 6}} />
                                    <ReferenceLine x={500} stroke="#f43f5e" strokeDasharray="5 5" />
                                </AreaChart>
                            </ResponsiveContainer>
                        </div>
                    </Card>
                  </div>
                )}
                
              </main>
            </div>
          );
        }

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>
