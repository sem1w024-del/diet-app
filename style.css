import React, { useEffect, useMemo, useState } from 'react';
import { createRoot } from 'react-dom/client';
import { Camera, Droplets, Flame, HeartPulse, Plus, Scale, Timer, Trash2, Utensils } from 'lucide-react';
import { CartesianGrid, Line, LineChart, ResponsiveContainer, Tooltip, XAxis, YAxis } from 'recharts';
import './style.css';

const STORAGE_KEY = 'my_private_diet_tracker_v1';

function today() {
  return new Date().toISOString().slice(0, 10);
}

function formatTime(seconds) {
  const h = Math.floor(seconds / 3600);
  const m = Math.floor((seconds % 3600) / 60);
  const s = seconds % 60;
  return `${String(h).padStart(2, '0')}:${String(m).padStart(2, '0')}:${String(s).padStart(2, '0')}`;
}

function Button({ children, onClick, variant = 'primary', className = '', type = 'button' }) {
  return <button type={type} onClick={onClick} className={`btn ${variant === 'outline' ? 'btn-outline' : 'btn-primary'} ${className}`}>{children}</button>;
}

function Card({ children }) {
  return <section className="card">{children}</section>;
}

function App() {
  const [tab, setTab] = useState('record');
  const [profile, setProfile] = useState({ height: '', weight: '' });
  const [weights, setWeights] = useState([]);
  const [water, setWater] = useState(0);
  const [foods, setFoods] = useState([]);
  const [foodName, setFoodName] = useState('');
  const [foodCal, setFoodCal] = useState('');
  const [timerRunning, setTimerRunning] = useState(false);
  const [fastSeconds, setFastSeconds] = useState(0);
  const [photo, setPhoto] = useState(null);

  useEffect(() => {
    const saved = localStorage.getItem(STORAGE_KEY);
    if (!saved) return;
    try {
      const parsed = JSON.parse(saved);
      setProfile(parsed.profile || { height: '', weight: '' });
      setWeights(parsed.weights || []);
      setWater(parsed.water || 0);
      setFoods(parsed.foods || []);
      setFastSeconds(parsed.fastSeconds || 0);
      setPhoto(parsed.photo || null);
    } catch {
      localStorage.removeItem(STORAGE_KEY);
    }
  }, []);

  useEffect(() => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify({ profile, weights, water, foods, fastSeconds, photo }));
  }, [profile, weights, water, foods, fastSeconds, photo]);

  useEffect(() => {
    if (!timerRunning) return undefined;
    const id = setInterval(() => setFastSeconds((v) => v + 1), 1000);
    return () => clearInterval(id);
  }, [timerRunning]);

  const bmi = useMemo(() => {
    const h = Number(profile.height) / 100;
    const w = Number(profile.weight);
    if (!h || !w) return null;
    return (w / (h * h)).toFixed(1);
  }, [profile]);

  const bmiStatus = useMemo(() => {
    if (!bmi) return '키와 몸무게를 입력해줘';
    const value = Number(bmi);
    if (value < 18.5) return '저체중';
    if (value < 23) return '정상';
    if (value < 25) return '과체중';
    return '비만';
  }, [bmi]);

  const totalCal = foods.reduce((sum, f) => sum + Number(f.cal || 0), 0);

  const saveWeight = () => {
    if (!profile.weight) return;
    const newRecord = { date: today(), weight: Number(profile.weight) };
    setWeights((prev) => {
      const filtered = prev.filter((item) => item.date !== today());
      return [...filtered, newRecord].sort((a, b) => a.date.localeCompare(b.date));
    });
  };

  const addFood = () => {
    if (!foodName.trim() || !foodCal) return;
    setFoods((prev) => [...prev, { id: Date.now(), name: foodName.trim(), cal: Number(foodCal), date: today() }]);
    setFoodName('');
    setFoodCal('');
  };

  const handlePhoto = (e) => {
    const file = e.target.files?.[0];
    if (!file) return;
    const reader = new FileReader();
    reader.onload = () => setPhoto(reader.result);
    reader.readAsDataURL(file);
  };

  return (
    <main className="page">
      <div className="app-shell">
        <header className="header">
          <h1>나의 다이어트 기록</h1>
          <p>개인 기록은 서버가 아니라 이 기기 안에 저장돼요.</p>
        </header>

        <div className="summary-grid">
          <Card>
            <div className="label"><HeartPulse size={16} /> BMI</div>
            <div className="big-number">{bmi || '--'}</div>
            <div className="muted">{bmiStatus}</div>
          </Card>
          <Card>
            <div className="label"><Flame size={16} /> 오늘 칼로리</div>
            <div className="big-number">{totalCal}</div>
            <div className="muted">kcal</div>
          </Card>
        </div>

        <nav className="tabs">
          {[['record', '기록'], ['timer', '공복'], ['food', '식단'], ['photo', '눈바디']].map(([value, label]) => (
            <button key={value} onClick={() => setTab(value)} className={tab === value ? 'active' : ''}>{label}</button>
          ))}
        </nav>

        {tab === 'record' && (
          <div className="stack">
            <Card>
              <div className="section-title"><Scale size={18} /> 신체 기록</div>
              <div className="input-grid">
                <label>키 cm<input inputMode="numeric" value={profile.height} onChange={(e) => setProfile({ ...profile, height: e.target.value })} placeholder="160" /></label>
                <label>몸무게 kg<input inputMode="decimal" value={profile.weight} onChange={(e) => setProfile({ ...profile, weight: e.target.value })} placeholder="55" /></label>
              </div>
              <Button onClick={saveWeight} className="full">오늘 몸무게 저장</Button>
            </Card>

            <Card>
              <div className="between"><div className="section-title"><Droplets size={18} /> 물 섭취</div><b>{water}잔</b></div>
              <div className="three-buttons">
                <Button variant="outline" onClick={() => setWater(Math.max(0, water - 1))}>-1</Button>
                <Button onClick={() => setWater(water + 1)}>+1잔</Button>
                <Button variant="outline" onClick={() => setWater(0)}>초기화</Button>
              </div>
            </Card>

            <Card>
              <div className="section-title">몸무게 변화</div>
              <div className="chart-box">
                {weights.length > 0 ? (
                  <ResponsiveContainer width="100%" height="100%">
                    <LineChart data={weights}>
                      <CartesianGrid strokeDasharray="3 3" />
                      <XAxis dataKey="date" fontSize={11} />
                      <YAxis domain={['dataMin - 2', 'dataMax + 2']} fontSize={11} />
                      <Tooltip />
                      <Line type="monotone" dataKey="weight" strokeWidth={3} dot={{ r: 4 }} />
                    </LineChart>
                  </ResponsiveContainer>
                ) : <div className="empty">몸무게를 저장하면 그래프가 보여요.</div>}
              </div>
            </Card>
          </div>
        )}

        {tab === 'timer' && (
          <Card>
            <div className="timer-card">
              <div className="circle"><Timer size={30} /></div>
              <div className="muted">공복 시간</div>
              <div className="timer-text">{formatTime(fastSeconds)}</div>
              <div className="two-buttons">
                <Button onClick={() => setTimerRunning(!timerRunning)}>{timerRunning ? '일시정지' : '시작'}</Button>
                <Button variant="outline" onClick={() => { setTimerRunning(false); setFastSeconds(0); }}>초기화</Button>
              </div>
              <p className="hint">식사를 시작하면 초기화하고, 식사 후 다시 시작하면 돼요.</p>
            </div>
          </Card>
        )}

        {tab === 'food' && (
          <div className="stack">
            <Card>
              <div className="section-title"><Utensils size={18} /> 음식 기록</div>
              <div className="input-grid">
                <input value={foodName} onChange={(e) => setFoodName(e.target.value)} placeholder="음식명" />
                <input inputMode="numeric" value={foodCal} onChange={(e) => setFoodCal(e.target.value)} placeholder="kcal" />
              </div>
              <Button onClick={addFood} className="full"><Plus size={16} /> 추가</Button>
            </Card>
            {foods.length === 0 ? <div className="empty-card">아직 기록된 음식이 없어요.</div> : foods.map((f) => (
              <div key={f.id} className="food-row">
                <div><b>{f.name}</b><span>{f.date}</span></div>
                <div className="food-cal"><b>{f.cal} kcal</b><button onClick={() => setFoods(foods.filter((item) => item.id !== f.id))}><Trash2 size={16} /></button></div>
              </div>
            ))}
          </div>
        )}

        {tab === 'photo' && (
          <Card>
            <div className="section-title"><Camera size={18} /> 눈바디 기록</div>
            <div className="photo-box">
              {photo ? <img src={photo} alt="눈바디 기록" /> : <p>사진은 서버로 올라가지 않고 이 기기에만 저장돼요.</p>}
            </div>
            <input className="file-input" type="file" accept="image/*" onChange={handlePhoto} />
            {photo && <Button variant="outline" className="full" onClick={() => setPhoto(null)}>사진 삭제</Button>}
          </Card>
        )}
      </div>
    </main>
  );
}

createRoot(document.getElementById('root')).render(<App />);
