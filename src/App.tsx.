import { useEffect, useMemo, useState } from 'react'
import {
  Activity, ArrowDownRight, ArrowUpRight, Boxes, ChartNoAxesCombined,
  CircleHelp, CreditCard, Languages, LayoutDashboard, Mic, MicOff,
  Plus, ReceiptText, Settings, ShoppingBag, Sparkles, TrendingUp,
  Wallet, Users, X
} from 'lucide-react'

type Kind = 'sale' | 'expense'
type Tx = { id: string; kind: Kind; amount: number; note: string; date: string }
type Due = { id: string; name: string; amount: number }
type Lang = 'en-IN' | 'hi-IN' | 'kn-IN'

const initialTx: Tx[] = [
  { id: 'seed-1', kind: 'sale', amount: 1250, note: 'Opening sample sale', date: new Date().toISOString() },
  { id: 'seed-2', kind: 'sale', amount: 1200, note: 'Opening sample sale', date: new Date().toISOString() },
  { id: 'seed-3', kind: 'expense', amount: 850, note: 'Opening sample expense', date: new Date().toISOString() }
]
const money = (n: number) => new Intl.NumberFormat('en-IN', { style: 'currency', currency: 'INR', maximumFractionDigits: 0 }).format(n)
const load = <T,>(key: string, fallback: T): T => {
  try { const value = localStorage.getItem(key); return value ? JSON.parse(value) as T : fallback } catch { return fallback }
}
const languageNames: Record<Lang, string> = { 'en-IN': 'English', 'hi-IN': 'हिन्दी', 'kn-IN': 'ಕನ್ನಡ' }

export default function App() {
  const [transactions, setTransactions] = useState<Tx[]>(() => load('bolo-transactions', initialTx))
  const [stock, setStock] = useState<number>(() => load('bolo-stock', 50))
  const [dues, setDues] = useState<Due[]>(() => load('bolo-dues', []))
  const [lang, setLang] = useState<Lang>('en-IN')
  const [listening, setListening] = useState(false)
  const [voiceMessage, setVoiceMessage] = useState('Tap the microphone and speak a simple business update.')
  const [manualKind, setManualKind] = useState<Kind>('sale')
  const [amount, setAmount] = useState('')
  const [note, setNote] = useState('')
  const [stockInput, setStockInput] = useState('')
  const [customer, setCustomer] = useState('')
  const [dueAmount, setDueAmount] = useState('')
  const [active, setActive] = useState('Overview')
  const [showHelp, setShowHelp] = useState(false)

  useEffect(() => { localStorage.setItem('bolo-transactions', JSON.stringify(transactions)) }, [transactions])
  useEffect(() => { localStorage.setItem('bolo-stock', JSON.stringify(stock)) }, [stock])
  useEffect(() => { localStorage.setItem('bolo-dues', JSON.stringify(dues)) }, [dues])

  const sales = useMemo(() => transactions.filter(t => t.kind === 'sale').reduce((a, t) => a + t.amount, 0), [transactions])
  const expenses = useMemo(() => transactions.filter(t => t.kind === 'expense').reduce((a, t) => a + t.amount, 0), [transactions])
  const profit = sales - expenses

  function addTransaction(kind: Kind, value: number, description: string) {
    if (!Number.isFinite(value) || value <= 0) return
    setTransactions(prev => [{ id: crypto.randomUUID(), kind, amount: value, note: description || (kind === 'sale' ? 'Sale' : 'Expense'), date: new Date().toISOString() }, ...prev])
    setVoiceMessage(`${kind === 'sale' ? 'Sale' : 'Expense'} recorded: ${money(value)}`)
  }

  function submitManual(e: React.FormEvent) {
    e.preventDefault()
    const value = Number(amount)
    if (value <= 0) return
    addTransaction(manualKind, value, note.trim() || 'Manual entry')
    setAmount(''); setNote('')
  }

  function addStock(e: React.FormEvent) {
    e.preventDefault()
    const value = Number(stockInput)
    if (value > 0) { setStock(prev => prev + value); setStockInput(''); setVoiceMessage(`Stock increased by ${value} units.`) }
  }

  function addDue(e: React.FormEvent) {
    e.preventDefault()
    const value = Number(dueAmount)
    if (customer.trim() && value > 0) {
      setDues(prev => [{ id: crypto.randomUUID(), name: customer.trim(), amount: value }, ...prev])
      setCustomer(''); setDueAmount('')
    }
  }

  function parseVoice(text: string) {
    const lower = text.toLowerCase()
    const amountMatch = lower.match(/(?:₹|rs\.?\s*|rupees?\s*)([\d,]+)|([\d,]+)\s*(?:rupees?|rs\.?)/i)
    const value = amountMatch ? Number((amountMatch[1] || amountMatch[2]).replace(/,/g, '')) : NaN
    const stockMatch = lower.match(/(?:add|increase|stock|inventory)\s+(\d+)\s*(?:items?|units?)?/i)
    if (/(stock|inventory|ಸ್ಟಾಕ್|स्टॉक)/i.test(lower) && stockMatch) {
      const count = Number(stockMatch[1]); setStock(prev => prev + count); setVoiceMessage(`Stock increased by ${count} units.`); return
    }
    if (Number.isFinite(value) && /(sale|sold|made|received|ವಿಕ್ರಯ|बेचा|बेची)/i.test(lower)) {
      addTransaction('sale', value, text); return
    }
    if (Number.isFinite(value) && /(expense|spent|paid|bought|खर्च|ಕೊಟ್ಟ|ಖರ್ಚು)/i.test(lower)) {
      addTransaction('expense', value, text); return
    }
    setVoiceMessage('I heard: “' + text + '”. Try “I made a sale of 250 rupees” or “Record an expense of 80 rupees”.')
  }

  function startVoice() {
    const SpeechRecognition = (window as Window & { SpeechRecognition?: new () => SpeechRecognitionLike; webkitSpeechRecognition?: new () => SpeechRecognitionLike }).SpeechRecognition
      || (window as Window & { webkitSpeechRecognition?: new () => SpeechRecognitionLike }).webkitSpeechRecognition
    if (!SpeechRecognition) { setVoiceMessage('Voice recognition is not available in this browser. Use the quick-entry form below.'); return }
    const recognition = new SpeechRecognition()
    recognition.lang = lang
    recognition.interimResults = false
    recognition.maxAlternatives = 1
    recognition.onstart = () => { setListening(true); setVoiceMessage('Listening… speak now.') }
    recognition.onresult = event => parseVoice(event.results[0][0].transcript)
    recognition.onerror = () => setVoiceMessage('I could not hear that clearly. Please try again or use quick entry.')
    recognition.onend = () => setListening(false)
    recognition.start()
  }

  const nav = [
    { label: 'Overview', icon: LayoutDashboard }, { label: 'Transactions', icon: ReceiptText },
    { label: 'Inventory', icon: Boxes }, { label: 'Customers', icon: Users }
  ]

  return <div className="app-shell">
    <aside className="sidebar">
      <div className="logo"><span className="logo-mark"><Mic size={21}/></span><span>BOLO<small>BUSINESS ASSISTANT</small></span></div>
      <div className="side-label">WORKSPACE</div>
      {nav.map(item => <button key={item.label} className={`nav-item ${active === item.label ? 'selected' : ''}`} onClick={() => setActive(item.label)}><item.icon size={18}/>{item.label}</button>)}
      <div className="side-bottom">
        <div className="help-card"><Sparkles size={18}/><b>Speak. Manage. Grow.</b><p>Simple business tracking, powered by your voice.</p><button onClick={() => setShowHelp(true)}>How BOLO works <ArrowUpRight size={14}/></button></div>
        <button className="nav-item" onClick={() => setShowHelp(true)}><Settings size={18}/>Settings & help</button>
      </div>
      <div className="profile"><div className="avatar">B</div><div><b>My Business</b><small>Demo workspace</small></div><span className="online-dot"/></div>
    </aside>

    <main className="main-content">
      <header className="topbar"><div className="mobile-brand">◉ BOLO</div><div className="crumb">Workspace <span>/</span> {active}</div><div className="top-actions"><label className="language"><Languages size={16}/><select value={lang} onChange={e => setLang(e.target.value as Lang)} aria-label="Speech language">{Object.entries(languageNames).map(([key, value]) => <option key={key} value={key}>{value}</option>)}</select></label><button className="icon-btn" onClick={() => setShowHelp(true)} aria-label="Help"><CircleHelp size={19}/></button><div className="avatar small">B</div></div></header>

      <div className="page">
        <section className="welcome-row"><div><div className="eyebrow">YOUR BUSINESS AT A GLANCE</div><h1>Good morning<span className="wave"> ✦</span></h1><p className="muted">A clearer view of your business starts here.</p></div><div className="date-chip"><Activity size={15}/> Live demo dashboard</div></section>

        <section className="voice-banner">
          <div className="voice-copy"><div className="voice-eyebrow"><span className="pulse"/> BOLO VOICE ASSISTANT</div><h2>Just say it.<br/><em>BOLO gets it done.</em></h2><p>Record a sale, expense or stock update with a simple voice command.</p><div className="voice-hint">Try: “I made a sale of 250 rupees”</div></div>
          <div className="voice-control"><button className={`mic-button ${listening ? 'listening' : ''}`} onClick={startVoice} disabled={listening} aria-label="Start voice command">{listening ? <MicOff size={30}/> : <Mic size={30}/>}</button><span>{listening ? 'Listening…' : 'Tap to speak'}</span><small>{languageNames[lang]}</small></div>
          <div className="voice-status">{voiceMessage}</div>
        </section>

        <section className="metric-grid">
          <Metric icon={<ShoppingBag/>} label="Total sales" value={money(sales)} tone="green" detail="Recorded income"/>
          <Metric icon={<Wallet/>} label="Total expenses" value={money(expenses)} tone="rose" detail="Recorded spending"/>
          <Metric icon={<TrendingUp/>} label="Estimated profit" value={money(profit)} tone="purple" detail="Sales minus expenses"/>
          <Metric icon={<Boxes/>} label="Stock items" value={String(stock)} tone="blue" detail="Current unit count"/>
        </section>

        <div className="section-heading"><div><h2>{active === 'Overview' ? 'Business overview' : active}</h2><p className="muted">Manage your day-to-day business activity.</p></div><span className="updated"><span className="pulse"/> Updates as you add records</span></div>

        <div className="content-grid">
          <section className="panel transactions-panel">
            <div className="panel-heading"><div><h3>Recent transactions</h3><p>Latest recorded activity</p></div><span className="tag">{transactions.length} records</span></div>
            {transactions.length === 0 ? <div className="empty">No transactions yet. Add your first sale or expense.</div> :
              <div className="transaction-list">{transactions.slice(0, 6).map(tx => <div className="transaction" key={tx.id}><div className={`transaction-icon ${tx.kind}`} >{tx.kind === 'sale' ? <ArrowDownRight size={17}/> : <ArrowUpRight size={17}/>}</div><div className="transaction-info"><b>{tx.note}</b><small>{new Date(tx.date).toLocaleString()}</small></div><strong className={tx.kind === 'sale' ? 'positive' : 'negative'}>{tx.kind === 'sale' ? '+' : '−'}{money(tx.amount)}</strong></div>)}</div>}
          </section>

          <section className="panel quick-panel">
            <div className="panel-heading"><div><h3>Quick entry</h3><p>Add a transaction manually</p></div><span className="quick-icon"><Plus size={17}/></span></div>
            <form onSubmit={submitManual}>
              <label>Transaction type</label><div className="segmented"><button type="button" className={manualKind === 'sale' ? 'active sale-active' : ''} onClick={() => setManualKind('sale')}>Sale</button><button type="button" className={manualKind === 'expense' ? 'active expense-active' : ''} onClick={() => setManualKind('expense')}>Expense</button></div>
              <label htmlFor="amount">Amount (₹)</label><input id="amount" type="number" min="1" required placeholder="e.g. 250" value={amount} onChange={e => setAmount(e.target.value)}/>
              <label htmlFor="note">Description</label><input id="note" placeholder="e.g. Rice sale" value={note} onChange={e => setNote(e.target.value)}/>
              <button className="primary-btn" type="submit">Add transaction <Plus size={16}/></button>
            </form>
          </section>
        </div>

        <div className="bottom-grid">
          <section className="panel mini-panel"><div className="panel-heading"><div><h3>Inventory update</h3><p>Add units to your stock count</p></div><Boxes size={20} className="muted-icon"/></div><form className="inline-form" onSubmit={addStock}><input aria-label="Stock units" type="number" min="1" required placeholder="Number of units" value={stockInput} onChange={e => setStockInput(e.target.value)}/><button className="secondary-btn" type="submit">Update stock</button></form></section>
          <section className="panel mini-panel"><div className="panel-heading"><div><h3>Customer payment</h3><p>Keep track of pending dues</p></div><CreditCard size={20} className="muted-icon"/></div><form className="due-form" onSubmit={addDue}><input aria-label="Customer name" required placeholder="Customer name" value={customer} onChange={e => setCustomer(e.target.value)}/><input aria-label="Due amount" type="number" min="1" required placeholder="Amount ₹" value={dueAmount} onChange={e => setDueAmount(e.target.value)}/><button className="secondary-btn" type="submit">Add due</button></form>{dues.length > 0 && <div className="due-list">{dues.slice(0,3).map(d => <div key={d.id}><span>{d.name}</span><b>{money(d.amount)}</b></div>)}</div>}</section>
        </div>
        <footer>BOLO MVP · Demo figures are stored in this browser · Built for small business owners</footer>
      </div>
    </main>

    {showHelp && <div className="modal-backdrop" onClick={() => setShowHelp(false)}><div className="modal" onClick={e => e.stopPropagation()}><button className="modal-close" onClick={() => setShowHelp(false)}><X size={18}/></button><div className="modal-icon"><Sparkles/></div><h2>Welcome to BOLO</h2><p>BOLO helps small business owners record everyday activity with less typing.</p><ul><li>Choose English, Hindi or Kannada for voice input.</li><li>Tap the microphone and say a simple sale, expense or stock command.</li><li>Use Quick entry if voice input is unavailable.</li><li>Check the dashboard for updated totals and estimated profit.</li></ul><div className="notice">Prototype note: information is saved only in this browser. Use sample data for demonstrations.</div><button className="primary-btn" onClick={() => setShowHelp(false)}>Got it</button></div></div>}
  </div>
}

function Metric({ icon, label, value, tone, detail }: { icon: React.ReactNode; label: string; value: string; tone: string; detail: string }) {
  return <article className="metric-card"><div className={`metric-icon ${tone}`}>{icon}</div><div className="metric-label">{label}</div><div className="metric-value">{value}</div><div className="metric-detail">{detail}</div></article>
}

type SpeechRecognitionLike = {
  lang: string; interimResults: boolean; maxAlternatives: number
  onstart: (() => void) | null
  onresult: ((event: { results: ArrayLike<ArrayLike<{ transcript: string }>> }) => void) | null
  onerror: (() => void) | null
  onend: (() => void) | null
  start: () => void
}
