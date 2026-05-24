# HyperMove
import React, { useEffect, useMemo, useRef, useState } from 'react'

// ===================================================== // HYPERMOVE PRO — APP FITNESS PROFISSIONAL COMPLETO // =====================================================

// --------------------------- // BANCO DE DADOS REALISTA // ---------------------------

const exerciseDB = [ { id: 1, name: 'Flexão', focus: 'Peitoral, tríceps e core', difficulty: 'Iniciante', equipment: 'Nenhum', instructions: 'Corpo reto, descer até quase encostar no chão.', image: 'https://images.unsplash.com/photo-1598971639058-bf9a5c3b7c2d', video: 'https://www.youtube.com/embed/IODxDxX7oi4', }, { id: 2, name: 'Agachamento', focus: 'Quadríceps e glúteos', difficulty: 'Iniciante', equipment: 'Nenhum', instructions: 'Descer até 90° mantendo postura reta.', image: 'https://images.unsplash.com/photo-1571019613914-85f342c1d9f6', video: 'https://www.youtube.com/embed/YaXPRqUwItQ', }, { id: 3, name: 'Supino', focus: 'Peitoral', difficulty: 'Intermediário', equipment: 'Academia', instructions: 'Descer barra controlada até o peito.', image: 'https://images.unsplash.com/photo-1581009146145-b5ef050c2e1e', video: 'https://www.youtube.com/embed/gRVjAtPip0Y', }, { id: 4, name: 'Box Jump', focus: 'Explosão', difficulty: 'Avançado', equipment: 'Caixa', instructions: 'Salto explosivo na caixa com aterrissagem suave.', image: 'https://images.unsplash.com/photo-1599058917212-d750089bc07e', video: 'https://www.youtube.com/embed/52r_Ul5k03g', }, ]

const workoutDB = [ { id: 1, title: 'Full Body Iniciante', level: 'Iniciante', rest: 60, exercises: [ { name: 'Flexão', sets: 3, reps: 10, rest: 60 }, { name: 'Agachamento', sets: 3, reps: 12, rest: 60 }, ], }, { id: 2, title: 'Hipertrofia Upper', level: 'Intermediário', rest: 90, exercises: [ { name: 'Supino', sets: 4, reps: 10, rest: 90 }, { name: 'Flexão', sets: 3, reps: 15, rest: 60 }, ], }, { id: 3, title: 'Explosão Elite', level: 'Avançado', rest: 120, exercises: [ { name: 'Box Jump', sets: 5, reps: 8, rest: 120 }, { name: 'Agachamento', sets: 4, reps: 12, rest: 90 }, ], }, ]

// --------------------------- // SIMULAÇÃO DE BANCO (LOCAL) // ---------------------------

const loadUser = () => { const data = localStorage.getItem('hypermove_user') return data ? JSON.parse(data) : null }

const saveUser = (user) => { localStorage.setItem('hypermove_user', JSON.stringify(user)) }

const saveProgress = (data) => { localStorage.setItem('hypermove_progress', JSON.stringify(data)) }

const loadProgress = () => { return JSON.parse(localStorage.getItem('hypermove_progress') || '[]') }

// --------------------------- // IA SIMPLES // ---------------------------

function aiWorkout(user) { if (user.level === 'Iniciante') return workoutDB[0] if (user.level === 'Intermediário') return workoutDB[1] return workoutDB[2] }

function aiExercise(exName) { return exerciseDB.find((e) => e.name === exName) }

// --------------------------- // APP // ---------------------------

export default function HyperMovePro() { const [user, setUser] = useState(loadUser() || { name: '', level: 'Iniciante' }) const [logged, setLogged] = useState(!!loadUser()) const [tab, setTab] = useState('home')

const [activeWorkout, setActiveWorkout] = useState(null) const [exIndex, setExIndex] = useState(0) const [setIndex, setSetIndex] = useState(1) const [rest, setRest] = useState(false) const [timer, setTimer] = useState(0) const [progress, setProgress] = useState(loadProgress())

const intervalRef = useRef(null)

useEffect(() => { if (activeWorkout) { intervalRef.current = setInterval(() => { setTimer((t) => t + 1) }, 1000) } return () => clearInterval(intervalRef.current) }, [activeWorkout])

const startWorkout = (w) => { setActiveWorkout(w) setExIndex(0) setSetIndex(1) setTimer(0) setTab('workout') }

const currentEx = activeWorkout?.exercises[exIndex] const exData = currentEx ? aiExercise(currentEx.name) : null

const next = () => { if (!activeWorkout) return

if (setIndex < currentEx.sets) {
  setSetIndex(setIndex + 1)
  setRest(true)
  setTimer(0)
  return
}

if (exIndex < activeWorkout.exercises.length - 1) {
  setExIndex(exIndex + 1)
  setSetIndex(1)
  setRest(true)
  setTimer(0)
  return
}

const updated = [...progress, { workout: activeWorkout.title, date: new Date() }]
setProgress(updated)
saveProgress(updated)

setActiveWorkout(null)
setTab('home')

}

// --------------------------- // LOGIN // ---------------------------

if (!logged) { return ( <div className="min-h-screen bg-black text-white flex items-center justify-center p-6"> <div className="w-full max-w-md bg-zinc-950 p-8 rounded-3xl border border-zinc-800"> <h1 className="text-4xl font-black text-red-500">HyperMove PRO</h1>

<input
        className="w-full mt-6 p-3 rounded-xl bg-zinc-900"
        placeholder="Nome"
        value={user.name}
        onChange={(e) => setUser({ ...user, name: e.target.value })}
      />

      <select
        className="w-full mt-4 p-3 rounded-xl bg-zinc-900"
        value={user.level}
        onChange={(e) => setUser({ ...user, level: e.target.value })}
      >
        <option>Iniciante</option>
        <option>Intermediário</option>
        <option>Avançado</option>
      </select>

      <button
        onClick={() => {
          setLogged(true)
          saveUser(user)
        }}
        className="w-full mt-6 bg-red-600 p-4 rounded-xl font-black"
      >
        Entrar
      </button>
    </div>
  </div>
)

}

// --------------------------- // TREINO ATIVO (APP REAL) // ---------------------------

if (tab === 'workout' && activeWorkout) { return ( <div className="min-h-screen bg-black text-white p-6"> <h1 className="text-3xl font-black text-red-500"> {activeWorkout.title} </h1>

<div className="grid md:grid-cols-2 gap-6 mt-6">
      <div className="bg-zinc-900 p-6 rounded-3xl">
        <h2 className="text-2xl font-bold">{currentEx.name}</h2>
        <p className="text-zinc-400 mt-2">{currentEx.sets} séries x {currentEx.reps} reps</p>

        <p className="mt-4 text-red-400">
          {rest ? 'Descanso' : 'Executando'}
        </p>

        <div className="text-5xl font-black mt-4">{timer}s</div>

        <button
          onClick={next}
          className="w-full mt-6 bg-red-600 p-4 rounded-2xl font-black"
        >
          Próximo
        </button>
      </div>

      <div className="bg-zinc-900 p-6 rounded-3xl">
        {exData && (
          <>
            <img src={exData.image} className="rounded-xl" />

            <iframe
              className="mt-4 w-full h-60 rounded-xl"
              src={exData.video}
              allowFullScreen
            />

            <p className="mt-4 text-zinc-300">
              {exData.instructions}
            </p>
          </>
        )}
      </div>
    </div>
  </div>
)

}

// --------------------------- // HOME // ---------------------------

const recommended = useMemo(() => aiWorkout(user), [user])

return ( <div className="min-h-screen bg-black text-white p-6"> <h1 className="text-4xl font-black text-red-500">HyperMove PRO</h1>

<p className="text-zinc-400">Olá {user.name}</p>

  <div className="mt-8 bg-zinc-900 p-6 rounded-3xl">
    <h2 className="text-2xl font-bold">Treino recomendado</h2>
    <p className="text-zinc-400 mt-2">{recommended.title}</p>

    <button
      onClick={() => startWorkout(recommended)}
      className="mt-6 w-full bg-red-600 p-4 rounded-2xl font-black"
    >
      Iniciar treino
    </button>
  </div>

  <div className="mt-10">
    <h2 className="text-xl font-bold">Histórico</h2>
    {progress.map((p, i) => (
      <div key={i} className="bg-zinc-900 p-4 mt-3 rounded-xl">
        {p.workout}
      </div>
    ))}
  </div>
</div>

) }
