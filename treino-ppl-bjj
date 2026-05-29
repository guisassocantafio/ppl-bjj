import { useState, useEffect, useCallback } from "react";

// ─── DATA ───────────────────────────────────────────────────────────────────

const WORKOUT_PLAN = {
  push: {
    label: "Push",
    emoji: "💪",
    color: "#FF6B35",
    exercises: [
      { id: "supino_reto", name: "Supino Reto", sets: 3, reps: "6-8" },
      { id: "supino_inclinado", name: "Supino Inclinado", sets: 3, reps: "8-10" },
      { id: "voador", name: "Voador", sets: 3, reps: "12" },
      { id: "desenvolvimento", name: "Desenvolvimento", sets: 3, reps: "8-10" },
      { id: "elevacao_lateral", name: "Elevação Lateral", sets: 3, reps: "10" },
      { id: "triceps_frances", name: "Tríceps Francês", sets: 3, reps: "10" },
      { id: "triceps_corda", name: "Tríceps Corda", sets: 3, reps: "10" },
    ],
  },
  pull: {
    label: "Pull",
    emoji: "🔙",
    color: "#4ECDC4",
    exercises: [
      { id: "puxada_frontal", name: "Puxada Frontal", sets: 3, reps: "8-10" },
      { id: "remada_curvada", name: "Remada Curvada", sets: 3, reps: "6-8" },
      { id: "pull_down", name: "Pull Down", sets: 3, reps: "12" },
      { id: "encolhimento_barra", name: "Encolhimento na Barra", sets: 3, reps: "10" },
      { id: "crucifixo_inverso", name: "Crucifixo Inverso", sets: 3, reps: "12" },
      { id: "rosca_direta", name: "Rosca Direta", sets: 3, reps: "10" },
      { id: "rosca_martelo", name: "Rosca Martelo", sets: 3, reps: "12" },
    ],
  },
  legs: {
    label: "Legs",
    emoji: "🦵",
    color: "#A855F7",
    exercises: [
      { id: "agachamento", name: "Agachamento", sets: 3, reps: "6-8" },
      { id: "leg_press", name: "Leg Press 45", sets: 3, reps: "8-10" },
      { id: "cadeira_extensora", name: "Cadeira Extensora", sets: 3, reps: "12" },
      { id: "mesa_flexora", name: "Mesa Flexora", sets: 3, reps: "10" },
      { id: "stiff", name: "Stiff", sets: 3, reps: "10" },
      { id: "cadeira_adutora", name: "Cadeira Adutora", sets: 3, reps: "10" },
      { id: "panturrilhas", name: "Panturrilhas em Pé", sets: 3, reps: "20" },
    ],
  },
};

const BELT_PROGRESSION = [
  { belt: "Branca", xp: 0, color: "#f5f5f5", text: "#111" },
  { belt: "Azul", xp: 500, color: "#2563EB", text: "#fff" },
  { belt: "Roxa", xp: 1500, color: "#7C3AED", text: "#fff" },
  { belt: "Marrom", xp: 3000, color: "#92400E", text: "#fff" },
  { belt: "Preta", xp: 6000, color: "#111", text: "#fff" },
];

function getBelt(xp) {
  let belt = BELT_PROGRESSION[0];
  for (const b of BELT_PROGRESSION) {
    if (xp >= b.xp) belt = b;
  }
  return belt;
}

function getNextBelt(xp) {
  for (const b of BELT_PROGRESSION) {
    if (xp < b.xp) return b;
  }
  return null;
}

// ─── STORAGE ────────────────────────────────────────────────────────────────

function useStorage(key, initial) {
  const [val, setVal] = useState(() => {
    try {
      const raw = localStorage.getItem(key);
      return raw ? JSON.parse(raw) : initial;
    } catch { return initial; }
  });
  const set = useCallback((v) => {
    setVal(prev => {
      const next = typeof v === "function" ? v(prev) : v;
      localStorage.setItem(key, JSON.stringify(next));
      return next;
    });
  }, [key]);
  return [val, set];
}

// ─── COMPONENTS ──────────────────────────────────────────────────────────────

function XPBar({ xp }) {
  const belt = getBelt(xp);
  const next = getNextBelt(xp);
  const pct = next ? Math.min(100, ((xp - belt.xp) / (next.xp - belt.xp)) * 100) : 100;
  return (
    <div style={{ background: "#1a1a2e", borderRadius: 16, padding: "12px 16px", marginBottom: 16 }}>
      <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 6 }}>
        <span style={{
          background: belt.color, color: belt.text, fontWeight: 800,
          padding: "3px 12px", borderRadius: 20, fontSize: 13, border: belt.belt === "Branca" ? "2px solid #555" : "none"
        }}>{belt.belt}</span>
        <span style={{ color: "#aaa", fontSize: 12 }}>{xp} XP{next ? ` / ${next.xp} XP` : " — Mestre!"}</span>
      </div>
      <div style={{ background: "#2a2a3e", borderRadius: 99, height: 8, overflow: "hidden" }}>
        <div style={{
          width: `${pct}%`, height: "100%",
          background: "linear-gradient(90deg, #FF6B35, #FF9500)",
          borderRadius: 99, transition: "width 0.6s ease"
        }} />
      </div>
      {next && <div style={{ color: "#666", fontSize: 11, marginTop: 4 }}>Próximo: Faixa {next.belt} em {next.xp - xp} XP</div>}
    </div>
  );
}

function Tab({ label, active, onClick, emoji }) {
  return (
    <button onClick={onClick} style={{
      flex: 1, padding: "10px 4px", border: "none", cursor: "pointer",
      background: active ? "#FF6B35" : "transparent",
      color: active ? "#fff" : "#888",
      fontWeight: active ? 800 : 500,
      fontSize: 11, borderRadius: 10,
      transition: "all 0.2s", display: "flex", flexDirection: "column", alignItems: "center", gap: 2
    }}>
      <span style={{ fontSize: 18 }}>{emoji}</span>
      {label}
    </button>
  );
}

function WorkoutTab({ logs, setLogs, xp, setXp }) {
  const [activeDay, setActiveDay] = useState("push");
  const [inputs, setInputs] = useState({});
  const [saved, setSaved] = useState(null);

  const plan = WORKOUT_PLAN[activeDay];

  function getLastLog(exId) {
    const dayLogs = (logs[activeDay] || []);
    for (let i = dayLogs.length - 1; i >= 0; i--) {
      const entry = dayLogs[i].exercises?.[exId];
      if (entry) return entry;
    }
    return null;
  }

  function handleSave() {
    const date = new Date().toLocaleDateString("pt-BR");
    const exercises = {};
    plan.exercises.forEach(ex => {
      if (inputs[ex.id]?.weight) {
        exercises[ex.id] = {
          weight: inputs[ex.id].weight,
          reps: inputs[ex.id].reps || ex.reps,
          date,
        };
      }
    });
    if (Object.keys(exercises).length === 0) return;

    setLogs(prev => ({
      ...prev,
      [activeDay]: [...(prev[activeDay] || []), { date, exercises }]
    }));
    setXp(x => x + 50);
    setInputs({});
    setSaved(activeDay);
    setTimeout(() => setSaved(null), 2000);
  }

  return (
    <div>
      <div style={{ display: "flex", gap: 8, marginBottom: 16 }}>
        {Object.entries(WORKOUT_PLAN).map(([key, val]) => (
          <button key={key} onClick={() => setActiveDay(key)} style={{
            flex: 1, padding: "10px 4px", border: `2px solid ${activeDay === key ? val.color : "#2a2a3e"}`,
            background: activeDay === key ? val.color + "22" : "#1a1a2e",
            color: activeDay === key ? val.color : "#888",
            borderRadius: 12, cursor: "pointer", fontWeight: 700, fontSize: 13,
            transition: "all 0.2s"
          }}>
            {val.emoji} {val.label}
          </button>
        ))}
      </div>

      <div style={{ display: "flex", flexDirection: "column", gap: 10 }}>
        {plan.exercises.map(ex => {
          const last = getLastLog(ex.id);
          return (
            <div key={ex.id} style={{
              background: "#1a1a2e", borderRadius: 14, padding: "12px 14px",
              border: "1px solid #2a2a3e"
            }}>
              <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start", marginBottom: 8 }}>
                <div>
                  <div style={{ color: "#fff", fontWeight: 700, fontSize: 14 }}>{ex.name}</div>
                  <div style={{ color: "#666", fontSize: 12 }}>{ex.sets}x{ex.reps} reps</div>
                </div>
                {last && (
                  <div style={{
                    background: "#0f3460", borderRadius: 8, padding: "4px 10px",
                    textAlign: "right"
                  }}>
                    <div style={{ color: "#4ECDC4", fontWeight: 800, fontSize: 15 }}>{last.weight}kg</div>
                    <div style={{ color: "#666", fontSize: 10 }}>{last.reps} • {last.date}</div>
                  </div>
                )}
              </div>
              <div style={{ display: "flex", gap: 8 }}>
                <input
                  type="number" placeholder="Carga (kg)"
                  value={inputs[ex.id]?.weight || ""}
                  onChange={e => setInputs(p => ({ ...p, [ex.id]: { ...p[ex.id], weight: e.target.value } }))}
                  style={{
                    flex: 2, background: "#0f0f1e", border: "1px solid #2a2a3e",
                    borderRadius: 8, padding: "8px 10px", color: "#fff", fontSize: 14,
                    outline: "none"
                  }}
                />
                <input
                  type="text" placeholder="Reps"
                  value={inputs[ex.id]?.reps || ""}
                  onChange={e => setInputs(p => ({ ...p, [ex.id]: { ...p[ex.id], reps: e.target.value } }))}
                  style={{
                    flex: 1, background: "#0f0f1e", border: "1px solid #2a2a3e",
                    borderRadius: 8, padding: "8px 10px", color: "#fff", fontSize: 14,
                    outline: "none"
                  }}
                />
              </div>
            </div>
          );
        })}
      </div>

      <button onClick={handleSave} style={{
        width: "100%", marginTop: 16, padding: "14px",
        background: saved ? "#22c55e" : `linear-gradient(135deg, ${plan.color}, ${plan.color}cc)`,
        border: "none", borderRadius: 14, color: "#fff",
        fontWeight: 800, fontSize: 16, cursor: "pointer",
        transition: "all 0.3s"
      }}>
        {saved ? "✅ Treino Salvo! +50 XP" : `💾 Salvar Treino ${plan.label}`}
      </button>
    </div>
  );
}

function BJJTab({ bjjLogs, setBjjLogs, xp, setXp }) {
  const [view, setView] = useState("rolls"); // rolls | tecnicas
  const [rollForm, setRollForm] = useState({ date: "", notes: "", result: "neutro", duration: "" });
  const [techForm, setTechForm] = useState({ name: "", category: "", notes: "" });
  const [saved, setSaved] = useState(false);

  function saveRoll() {
    if (!rollForm.notes) return;
    const date = rollForm.date || new Date().toLocaleDateString("pt-BR");
    setBjjLogs(prev => ({
      ...prev,
      rolls: [...(prev.rolls || []), { ...rollForm, date, id: Date.now() }]
    }));
    setXp(x => x + 30);
    setRollForm({ date: "", notes: "", result: "neutro", duration: "" });
    setSaved("roll");
    setTimeout(() => setSaved(false), 2000);
  }

  function saveTech() {
    if (!techForm.name) return;
    setBjjLogs(prev => ({
      ...prev,
      tecnicas: [...(prev.tecnicas || []), { ...techForm, date: new Date().toLocaleDateString("pt-BR"), id: Date.now() }]
    }));
    setXp(x => x + 20);
    setTechForm({ name: "", category: "", notes: "" });
    setSaved("tech");
    setTimeout(() => setSaved(false), 2000);
  }

  const rolls = bjjLogs.rolls || [];
  const tecnicas = bjjLogs.tecnicas || [];
  const resultColors = { vitoria: "#22c55e", derrota: "#ef4444", neutro: "#888" };
  const resultLabels = { vitoria: "🏆 Vitória", derrota: "😤 Aprendi", neutro: "🤝 Neutro" };

  return (
    <div>
      <div style={{ display: "flex", gap: 8, marginBottom: 16 }}>
        {["rolls", "tecnicas"].map(v => (
          <button key={v} onClick={() => setView(v)} style={{
            flex: 1, padding: 10, border: `2px solid ${view === v ? "#e91e8c" : "#2a2a3e"}`,
            background: view === v ? "#e91e8c22" : "#1a1a2e",
            color: view === v ? "#e91e8c" : "#888",
            borderRadius: 12, cursor: "pointer", fontWeight: 700,
            fontSize: 14, transition: "all 0.2s"
          }}>
            {v === "rolls" ? "🥋 Rolês" : "📚 Técnicas"}
          </button>
        ))}
      </div>

      {view === "rolls" && (
        <>
          <div style={{ background: "#1a1a2e", borderRadius: 14, padding: 14, marginBottom: 16, border: "1px solid #2a2a3e" }}>
            <div style={{ color: "#e91e8c", fontWeight: 700, marginBottom: 10 }}>📝 Novo Rolê</div>
            <div style={{ display: "flex", gap: 8, marginBottom: 8 }}>
              {["vitoria", "neutro", "derrota"].map(r => (
                <button key={r} onClick={() => setRollForm(p => ({ ...p, result: r }))} style={{
                  flex: 1, padding: "6px 4px", border: `2px solid ${rollForm.result === r ? resultColors[r] : "#2a2a3e"}`,
                  background: rollForm.result === r ? resultColors[r] + "33" : "transparent",
                  color: rollForm.result === r ? resultColors[r] : "#666",
                  borderRadius: 8, cursor: "pointer", fontSize: 11, fontWeight: 700
                }}>{resultLabels[r]}</button>
              ))}
            </div>
            <input type="text" placeholder="Duração (ex: 1h30)"
              value={rollForm.duration}
              onChange={e => setRollForm(p => ({ ...p, duration: e.target.value }))}
              style={{ width: "100%", marginBottom: 8, background: "#0f0f1e", border: "1px solid #2a2a3e", borderRadius: 8, padding: "8px 10px", color: "#fff", fontSize: 13, outline: "none", boxSizing: "border-box" }}
            />
            <textarea placeholder="Resumo do rolê: o que funcionou, o que precisa melhorar..."
              value={rollForm.notes}
              onChange={e => setRollForm(p => ({ ...p, notes: e.target.value }))}
              style={{ width: "100%", background: "#0f0f1e", border: "1px solid #2a2a3e", borderRadius: 8, padding: "8px 10px", color: "#fff", fontSize: 13, resize: "vertical", minHeight: 80, outline: "none", boxSizing: "border-box" }}
            />
            <button onClick={saveRoll} style={{
              width: "100%", marginTop: 8, padding: "12px",
              background: saved === "roll" ? "#22c55e" : "linear-gradient(135deg, #e91e8c, #9333ea)",
              border: "none", borderRadius: 12, color: "#fff", fontWeight: 800, fontSize: 14, cursor: "pointer"
            }}>
              {saved === "roll" ? "✅ Salvo! +30 XP" : "💾 Salvar Rolê"}
            </button>
          </div>
          <div style={{ display: "flex", flexDirection: "column", gap: 8 }}>
            {[...rolls].reverse().map(r => (
              <div key={r.id} style={{
                background: "#1a1a2e", borderRadius: 12, padding: "10px 14px",
                borderLeft: `4px solid ${resultColors[r.result]}`
              }}>
                <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 4 }}>
                  <span style={{ color: resultColors[r.result], fontWeight: 700, fontSize: 13 }}>{resultLabels[r.result]}</span>
                  <span style={{ color: "#555", fontSize: 12 }}>{r.date}{r.duration ? ` • ${r.duration}` : ""}</span>
                </div>
                <div style={{ color: "#ccc", fontSize: 13 }}>{r.notes}</div>
              </div>
            ))}
            {rolls.length === 0 && <div style={{ textAlign: "center", color: "#555", padding: 20 }}>Nenhum rolê registrado ainda 🥋</div>}
          </div>
        </>
      )}

      {view === "tecnicas" && (
        <>
          <div style={{ background: "#1a1a2e", borderRadius: 14, padding: 14, marginBottom: 16, border: "1px solid #2a2a3e" }}>
            <div style={{ color: "#e91e8c", fontWeight: 700, marginBottom: 10 }}>📚 Nova Técnica</div>
            <input type="text" placeholder="Nome da técnica (ex: Triangle Choke)"
              value={techForm.name}
              onChange={e => setTechForm(p => ({ ...p, name: e.target.value }))}
              style={{ width: "100%", marginBottom: 8, background: "#0f0f1e", border: "1px solid #2a2a3e", borderRadius: 8, padding: "8px 10px", color: "#fff", fontSize: 13, outline: "none", boxSizing: "border-box" }}
            />
            <input type="text" placeholder="Categoria (ex: Guarda, Finalização, Passagem)"
              value={techForm.category}
              onChange={e => setTechForm(p => ({ ...p, category: e.target.value }))}
              style={{ width: "100%", marginBottom: 8, background: "#0f0f1e", border: "1px solid #2a2a3e", borderRadius: 8, padding: "8px 10px", color: "#fff", fontSize: 13, outline: "none", boxSizing: "border-box" }}
            />
            <textarea placeholder="Detalhes, pontos chave, dicas do professor..."
              value={techForm.notes}
              onChange={e => setTechForm(p => ({ ...p, notes: e.target.value }))}
              style={{ width: "100%", background: "#0f0f1e", border: "1px solid #2a2a3e", borderRadius: 8, padding: "8px 10px", color: "#fff", fontSize: 13, resize: "vertical", minHeight: 70, outline: "none", boxSizing: "border-box" }}
            />
            <button onClick={saveTech} style={{
              width: "100%", marginTop: 8, padding: "12px",
              background: saved === "tech" ? "#22c55e" : "linear-gradient(135deg, #e91e8c, #9333ea)",
              border: "none", borderRadius: 12, color: "#fff", fontWeight: 800, fontSize: 14, cursor: "pointer"
            }}>
              {saved === "tech" ? "✅ Salvo! +20 XP" : "💾 Salvar Técnica"}
            </button>
          </div>
          <div style={{ display: "flex", flexDirection: "column", gap: 8 }}>
            {[...tecnicas].reverse().map(t => (
              <div key={t.id} style={{ background: "#1a1a2e", borderRadius: 12, padding: "10px 14px", border: "1px solid #2a2a3e" }}>
                <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 4 }}>
                  <span style={{ color: "#fff", fontWeight: 700, fontSize: 14 }}>{t.name}</span>
                  {t.category && <span style={{ background: "#9333ea33", color: "#9333ea", padding: "2px 8px", borderRadius: 6, fontSize: 11, fontWeight: 700 }}>{t.category}</span>}
                </div>
                {t.notes && <div style={{ color: "#aaa", fontSize: 13 }}>{t.notes}</div>}
                <div style={{ color: "#555", fontSize: 11, marginTop: 4 }}>{t.date}</div>
              </div>
            ))}
            {tecnicas.length === 0 && <div style={{ textAlign: "center", color: "#555", padding: 20 }}>Nenhuma técnica registrada ainda 📚</div>}
          </div>
        </>
      )}
    </div>
  );
}

function StatsTab({ logs, bjjLogs, xp }) {
  const totalWorkouts = Object.values(logs).reduce((a, v) => a + v.length, 0);
  const pushCount = (logs.push || []).length;
  const pullCount = (logs.pull || []).length;
  const legsCount = (logs.legs || []).length;
  const rollsCount = (bjjLogs.rolls || []).length;
  const techsCount = (bjjLogs.tecnicas || []).length;
  const wins = (bjjLogs.rolls || []).filter(r => r.result === "vitoria").length;
  const belt = getBelt(xp);

  const allExercises = {};
  Object.entries(logs).forEach(([day, sessions]) => {
    WORKOUT_PLAN[day].exercises.forEach(ex => {
      const allWeights = sessions.flatMap(s => s.exercises?.[ex.id] ? [parseFloat(s.exercises[ex.id].weight)] : []);
      if (allWeights.length > 0) allExercises[ex.name] = Math.max(...allWeights);
    });
  });
  const prs = Object.entries(allExercises).sort((a, b) => b[1] - a[1]).slice(0, 5);

  const streakDays = totalWorkouts; // simplified
  const level = Math.floor(xp / 100) + 1;

  return (
    <div>
      <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 10, marginBottom: 16 }}>
        {[
          { label: "Treinos Totais", val: totalWorkouts, icon: "🏋️", color: "#FF6B35" },
          { label: "XP Total", val: xp, icon: "⚡", color: "#FFD700" },
          { label: "Nível", val: level, icon: "🎯", color: "#4ECDC4" },
          { label: "Faixa Atual", val: belt.belt, icon: "🥋", color: "#e91e8c" },
          { label: "Rolês BJJ", val: rollsCount, icon: "🥊", color: "#9333ea" },
          { label: "Técnicas", val: techsCount, icon: "📚", color: "#22c55e" },
        ].map(s => (
          <div key={s.label} style={{
            background: "#1a1a2e", borderRadius: 14, padding: "12px 14px",
            border: `1px solid ${s.color}33`
          }}>
            <div style={{ fontSize: 22 }}>{s.icon}</div>
            <div style={{ color: s.color, fontWeight: 800, fontSize: 22 }}>{s.val}</div>
            <div style={{ color: "#666", fontSize: 11 }}>{s.label}</div>
          </div>
        ))}
      </div>

      <div style={{ background: "#1a1a2e", borderRadius: 14, padding: 14, marginBottom: 12, border: "1px solid #2a2a3e" }}>
        <div style={{ color: "#FF6B35", fontWeight: 700, marginBottom: 10 }}>📊 Divisão PPL</div>
        {[
          { key: "push", label: "Push 💪", count: pushCount, color: "#FF6B35" },
          { key: "pull", label: "Pull 🔙", count: pullCount, color: "#4ECDC4" },
          { key: "legs", label: "Legs 🦵", count: legsCount, color: "#A855F7" },
        ].map(d => {
          const pct = totalWorkouts > 0 ? (d.count / totalWorkouts) * 100 : 0;
          return (
            <div key={d.key} style={{ marginBottom: 8 }}>
              <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 3 }}>
                <span style={{ color: "#ccc", fontSize: 13 }}>{d.label}</span>
                <span style={{ color: d.color, fontSize: 13, fontWeight: 700 }}>{d.count}x</span>
              </div>
              <div style={{ background: "#0f0f1e", borderRadius: 99, height: 6 }}>
                <div style={{ width: `${pct}%`, height: "100%", background: d.color, borderRadius: 99 }} />
              </div>
            </div>
          );
        })}
      </div>

      {prs.length > 0 && (
        <div style={{ background: "#1a1a2e", borderRadius: 14, padding: 14, marginBottom: 12, border: "1px solid #FFD70033" }}>
          <div style={{ color: "#FFD700", fontWeight: 700, marginBottom: 10 }}>🏆 Top 5 Cargas (PR)</div>
          {prs.map(([name, kg], i) => (
            <div key={name} style={{ display: "flex", justifyContent: "space-between", padding: "6px 0", borderBottom: i < prs.length - 1 ? "1px solid #2a2a3e" : "none" }}>
              <span style={{ color: "#ccc", fontSize: 13 }}>{name}</span>
              <span style={{ color: "#FFD700", fontWeight: 800, fontSize: 14 }}>{kg}kg</span>
            </div>
          ))}
        </div>
      )}

      {rollsCount > 0 && (
        <div style={{ background: "#1a1a2e", borderRadius: 14, padding: 14, border: "1px solid #e91e8c33" }}>
          <div style={{ color: "#e91e8c", fontWeight: 700, marginBottom: 10 }}>🥋 BJJ Win Rate</div>
          <div style={{ display: "flex", gap: 12 }}>
            {[
              { label: "Vitórias", count: wins, color: "#22c55e" },
              { label: "Aprendi", count: (bjjLogs.rolls || []).filter(r => r.result === "derrota").length, color: "#ef4444" },
              { label: "Neutros", count: (bjjLogs.rolls || []).filter(r => r.result === "neutro").length, color: "#888" },
            ].map(s => (
              <div key={s.label} style={{ flex: 1, textAlign: "center" }}>
                <div style={{ color: s.color, fontWeight: 800, fontSize: 22 }}>{s.count}</div>
                <div style={{ color: "#666", fontSize: 11 }}>{s.label}</div>
              </div>
            ))}
          </div>
        </div>
      )}
    </div>
  );
}

function GamificationTab({ xp, setXp, logs, bjjLogs }) {
  const totalWorkouts = Object.values(logs).reduce((a, v) => a + v.length, 0);
  const rollsCount = (bjjLogs.rolls || []).length;
  const techsCount = (bjjLogs.tecnicas || []).length;
  const belt = getBelt(xp);
  const level = Math.floor(xp / 100) + 1;

  const achievements = [
    { id: "first_workout", icon: "🔥", title: "Primeira Suada", desc: "Complete seu primeiro treino", done: totalWorkouts >= 1, xp: 50 },
    { id: "ten_workouts", icon: "💪", title: "Consistência", desc: "10 treinos completados", done: totalWorkouts >= 10, xp: 200 },
    { id: "fifty_workouts", icon: "🏆", title: "Atleta Dedicado", desc: "50 treinos completados", done: totalWorkouts >= 50, xp: 1000 },
    { id: "first_bjj", icon: "🥋", title: "Oss!", desc: "Primeiro rolê registrado", done: rollsCount >= 1, xp: 30 },
    { id: "ten_bjj", icon: "🎽", title: "Tapout Artist", desc: "10 rolês registrados", done: rollsCount >= 10, xp: 150 },
    { id: "five_tech", icon: "📚", title: "Enciclopédia Viva", desc: "5 técnicas aprendidas", done: techsCount >= 5, xp: 100 },
    { id: "twenty_tech", icon: "🧠", title: "Professor Honorário", desc: "20 técnicas aprendidas", done: techsCount >= 20, xp: 500 },
    { id: "push_five", icon: "👊", title: "Peito de Ferro", desc: "5 treinos Push", done: (logs.push || []).length >= 5, xp: 100 },
    { id: "legs_five", icon: "🦵", title: "Pernas de Aço", desc: "5 treinos de Legs", done: (logs.legs || []).length >= 5, xp: 100 },
    { id: "blue_belt", icon: "🔵", title: "Faixa Azul", desc: "Alcance 500 XP", done: xp >= 500, xp: 0 },
    { id: "purple_belt", icon: "🟣", title: "Faixa Roxa", desc: "Alcance 1500 XP", done: xp >= 1500, xp: 0 },
  ];

  const done = achievements.filter(a => a.done).length;

  const challenges = [
    { icon: "⚡", title: "Treino hoje", desc: `${totalWorkouts % 3}/3 sessões PPL completadas`, pct: Math.min(100, ((totalWorkouts % 3) / 3) * 100) },
    { icon: "🥋", title: "Semana BJJ", desc: `${rollsCount % 4}/4 rolês registrados`, pct: Math.min(100, ((rollsCount % 4) / 4) * 100) },
    { icon: "📚", title: "Aprendizado", desc: `${techsCount % 3}/3 técnicas esta semana`, pct: Math.min(100, ((techsCount % 3) / 3) * 100) },
  ];

  return (
    <div>
      <div style={{ background: "linear-gradient(135deg, #1a1a2e, #0f3460)", borderRadius: 16, padding: 16, marginBottom: 16, textAlign: "center", border: "1px solid #FFD70033" }}>
        <div style={{ fontSize: 48, marginBottom: 4 }}>⚔️</div>
        <div style={{ color: "#FFD700", fontWeight: 900, fontSize: 28 }}>Nível {level}</div>
        <div style={{
          display: "inline-block", background: belt.color, color: belt.text,
          padding: "4px 16px", borderRadius: 20, fontWeight: 800, fontSize: 14, marginTop: 4,
          border: belt.belt === "Branca" ? "2px solid #555" : "none"
        }}>Faixa {belt.belt}</div>
        <div style={{ color: "#888", marginTop: 8, fontSize: 13 }}>{xp} XP total • {done}/{achievements.length} conquistas</div>
      </div>

      <div style={{ color: "#FFD700", fontWeight: 700, marginBottom: 8, fontSize: 14 }}>🎯 Desafios Ativos</div>
      <div style={{ display: "flex", flexDirection: "column", gap: 8, marginBottom: 16 }}>
        {challenges.map(c => (
          <div key={c.title} style={{ background: "#1a1a2e", borderRadius: 12, padding: "10px 14px", border: "1px solid #2a2a3e" }}>
            <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 4 }}>
              <span style={{ color: "#ccc", fontWeight: 600 }}>{c.icon} {c.title}</span>
              <span style={{ color: "#888", fontSize: 12 }}>{c.desc}</span>
            </div>
            <div style={{ background: "#0f0f1e", borderRadius: 99, height: 6 }}>
              <div style={{ width: `${c.pct}%`, height: "100%", background: "linear-gradient(90deg,#FF6B35,#FFD700)", borderRadius: 99, transition: "width 0.5s" }} />
            </div>
          </div>
        ))}
      </div>

      <div style={{ color: "#FFD700", fontWeight: 700, marginBottom: 8, fontSize: 14 }}>🏆 Conquistas</div>
      <div style={{ display: "flex", flexDirection: "column", gap: 8 }}>
        {achievements.map(a => (
          <div key={a.id} style={{
            background: a.done ? "#1a2e1a" : "#1a1a2e",
            borderRadius: 12, padding: "10px 14px",
            border: `1px solid ${a.done ? "#22c55e44" : "#2a2a3e"}`,
            opacity: a.done ? 1 : 0.6,
            display: "flex", alignItems: "center", gap: 12
          }}>
            <span style={{ fontSize: 24 }}>{a.done ? a.icon : "🔒"}</span>
            <div style={{ flex: 1 }}>
              <div style={{ color: a.done ? "#22c55e" : "#888", fontWeight: 700, fontSize: 13 }}>{a.title}</div>
              <div style={{ color: "#555", fontSize: 12 }}>{a.desc}</div>
            </div>
            {a.xp > 0 && <span style={{ color: "#FFD700", fontWeight: 800, fontSize: 13 }}>+{a.xp}XP</span>}
          </div>
        ))}
      </div>
    </div>
  );
}

// ─── MAIN APP ─────────────────────────────────────────────────────────────────

export default function App() {
  const [tab, setTab] = useState("workout");
  const [logs, setLogs] = useStorage("ppl_logs", { push: [], pull: [], legs: [] });
  const [bjjLogs, setBjjLogs] = useStorage("bjj_logs", { rolls: [], tecnicas: [] });
  const [xp, setXp] = useStorage("user_xp", 0);

  const tabs = [
    { id: "workout", label: "Treino", emoji: "🏋️" },
    { id: "bjj", label: "BJJ", emoji: "🥋" },
    { id: "stats", label: "Stats", emoji: "📊" },
    { id: "gamification", label: "XP", emoji: "⚔️" },
  ];

  return (
    <div style={{
      minHeight: "100vh", background: "#0a0a14",
      fontFamily: "'Segoe UI', system-ui, sans-serif",
      color: "#fff", maxWidth: 480, margin: "0 auto",
      display: "flex", flexDirection: "column"
    }}>
      {/* Header */}
      <div style={{
        padding: "20px 16px 12px",
        background: "linear-gradient(180deg, #0f0f1e 0%, #0a0a14 100%)",
        borderBottom: "1px solid #1a1a2e",
        position: "sticky", top: 0, zIndex: 10
      }}>
        <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 10 }}>
          <div>
            <div style={{ fontWeight: 900, fontSize: 20, letterSpacing: -0.5 }}>
              <span style={{ color: "#FF6B35" }}>PPL</span>
              <span style={{ color: "#fff" }}> + </span>
              <span style={{ color: "#e91e8c" }}>BJJ</span>
            </div>
            <div style={{ color: "#666", fontSize: 12 }}>Tracker Pro</div>
          </div>
          <div style={{ textAlign: "right" }}>
            <div style={{ color: "#FFD700", fontWeight: 800, fontSize: 16 }}>⚡ {xp} XP</div>
            <div style={{ color: "#666", fontSize: 11 }}>Nível {Math.floor(xp / 100) + 1}</div>
          </div>
        </div>
        <XPBar xp={xp} />
        <div style={{ display: "flex", gap: 6, background: "#1a1a2e", borderRadius: 14, padding: 4 }}>
          {tabs.map(t => <Tab key={t.id} label={t.label} emoji={t.emoji} active={tab === t.id} onClick={() => setTab(t.id)} />)}
        </div>
      </div>

      {/* Content */}
      <div style={{ flex: 1, padding: 16, paddingBottom: 32 }}>
        {tab === "workout" && <WorkoutTab logs={logs} setLogs={setLogs} xp={xp} setXp={setXp} />}
        {tab === "bjj" && <BJJTab bjjLogs={bjjLogs} setBjjLogs={setBjjLogs} xp={xp} setXp={setXp} />}
        {tab === "stats" && <StatsTab logs={logs} bjjLogs={bjjLogs} xp={xp} />}
        {tab === "gamification" && <GamificationTab xp={xp} setXp={setXp} logs={logs} bjjLogs={bjjLogs} />}
      </div>
    </div>
  );
}
