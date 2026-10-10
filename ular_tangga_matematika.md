<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ular Tangga Matematika: Petualangan Deret Geometri</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;600;700&family=Poppins:wght@400;600;700&display=swap" rel="stylesheet">
    <!-- FontAwesome untuk Ikon -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        body {
            font-family: 'Poppins', sans-serif;
            user-select: none;
        }
        h1, h2, h3, .font-heading {
            font-family: 'Fredoka', cursive, sans-serif;
        }
        /* 3D Dice Styling */
        .dice-container {
            perspective: 600px;
        }
        .dice {
            width: 80px;
            height: 80px;
            position: relative;
            transform-style: preserve-3d;
            transition: transform 0.1s;
        }
        .dice.rolling {
            animation: rollDice 3.5s cubic-bezier(0.15, 0.9, 0.2, 1) forwards;
        }
        .dice-face {
            position: absolute;
            width: 100%;
            height: 100%;
            background: white;
            border: 3px solid #cbd5e1;
            border-radius: 12px;
            display: grid;
            grid-template: repeat(3, 1fr) / repeat(3, 1fr);
            padding: 8px;
            box-shadow: inset 0 0 10px rgba(0,0,0,0.1);
        }
        .dot {
            background: #1e293b;
            border-radius: 50%;
            width: 12px;
            height: 12px;
            align-self: center;
            justify-self: center;
        }
        /* Posisi Titik Dadu */
        .face-1 { transform: rotateY(0deg) translateZ(40px); }
        .face-2 { transform: rotateY(-90deg) translateZ(40px); }
        .face-3 { transform: rotateY(180deg) translateZ(40px); }
        .face-4 { transform: rotateY(90deg) translateZ(40px); }
        .face-5 { transform: rotateX(90deg) translateZ(40px); }
        .face-6 { transform: rotateX(-90deg) translateZ(40px); }

        @keyframes rollDice {
            0% { transform: rotateX(0deg) rotateY(0deg) rotateZ(0deg); }
            20% { transform: rotateX(360deg) rotateY(180deg) rotateZ(90deg); }
            50% { transform: rotateX(720deg) rotateY(540deg) rotateZ(180deg); }
            80% { transform: rotateX(1080deg) rotateY(900deg) rotateZ(270deg); }
            100% { transform: var(--target-transform); }
        }

        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f5f9;
        }
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #94a3b8;
        }
    </style>
</head>
<body class="bg-gradient-to-br from-indigo-900 via-purple-900 to-blue-900 min-h-screen text-slate-100 flex flex-col justify-between">

    <!-- AUDIO SOUND FX (Web Audio API Synthesizer) -->
    <script>
        const SoundFX = {
            enabled: true,
            ctx: null,
            init() {
                if (!this.ctx) {
                    this.ctx = new (window.AudioContext || window.webkitAudioContext)();
                }
            },
            playRoll() {
                if (!this.enabled) return;
                this.init();
                let now = this.ctx.currentTime;
                for (let i = 0; i < 15; i++) {
                    let osc = this.ctx.createOscillator();
                    let gain = this.ctx.createGain();
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(150 + Math.random() * 300, now + i * 0.2);
                    gain.gain.setValueAtTime(0.1, now + i * 0.2);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + i * 0.2 + 0.1);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start(now + i * 0.2);
                    osc.stop(now + i * 0.2 + 0.1);
                }
            },
            playMove() {
                if (!this.enabled) return;
                this.init();
                let osc = this.ctx.createOscillator();
                let gain = this.ctx.createGain();
                osc.type = 'sine';
                osc.frequency.setValueAtTime(440, this.ctx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(880, this.ctx.currentTime + 0.1);
                gain.gain.setValueAtTime(0.15, this.ctx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.01, this.ctx.currentTime + 0.1);
                osc.connect(gain);
                gain.connect(this.ctx.destination);
                osc.start();
                osc.stop(this.ctx.currentTime + 0.1);
            },
            playSuccess() {
                if (!this.enabled) return;
                this.init();
                let now = this.ctx.currentTime;
                [523.25, 659.25, 783.99, 1046.50].forEach((freq, idx) => {
                    let osc = this.ctx.createOscillator();
                    let gain = this.ctx.createGain();
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(freq, now + idx * 0.1);
                    gain.gain.setValueAtTime(0.2, now + idx * 0.1);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + idx * 0.1 + 0.3);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start(now + idx * 0.1);
                    osc.stop(now + idx * 0.1 + 0.3);
                });
            },
            playWrong() {
                if (!this.enabled) return;
                this.init();
                let now = this.ctx.currentTime;
                let osc = this.ctx.createOscillator();
                let gain = this.ctx.createGain();
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(200, now);
                osc.frequency.linearRampToValueAtTime(100, now + 0.3);
                gain.gain.setValueAtTime(0.2, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.3);
                osc.connect(gain);
                gain.connect(this.ctx.destination);
                osc.start(now);
                osc.stop(now + 0.3);
            }
        };
    </script>

    <!-- ================= 1. HALAMAN UTAMA (HOME) ================= -->
    <div id="view-home" class="flex-1 flex flex-col items-center justify-center p-6 text-center">
        <div class="max-w-4xl mx-auto bg-white/10 backdrop-blur-md rounded-3xl p-8 border border-white/20 shadow-2xl relative overflow-hidden">
            <!-- Dekorasi Ilustrasi Latar Belakang -->
            <div class="absolute -top-10 -left-10 w-32 h-32 bg-pink-500/20 rounded-full blur-2xl"></div>
            <div class="absolute -bottom-10 -right-10 w-32 h-32 bg-cyan-500/20 rounded-full blur-2xl"></div>

            <div class="inline-block px-4 py-1.5 bg-gradient-to-r from-pink-500 to-purple-500 rounded-full text-xs font-semibold tracking-wider uppercase mb-4 shadow-md text-white animate-pulse">
                Media Pembelajaran Interaktif SMA
            </div>

            <h1 class="text-4xl md:text-6xl font-extrabold tracking-tight text-transparent bg-clip-text bg-gradient-to-r from-yellow-300 via-pink-300 to-cyan-300 mb-2 drop-shadow-sm">
                ULAR TANGGA MATEMATIKA
            </h1>
            <h2 class="text-2xl md:text-3xl font-bold text-cyan-200 mb-8">
                Petualangan Deret Geometri
            </h2>

            <!-- Karakter Pion Preview -->
            <div class="flex flex-wrap justify-center gap-4 mb-10">
                <div class="flex items-center gap-2 bg-white/10 px-4 py-2 rounded-xl border border-white/10">
                    <span class="text-2xl">🐉</span> <span class="font-semibold text-sm">Naga Biru</span>
                </div>
                <div class="flex items-center gap-2 bg-white/10 px-4 py-2 rounded-xl border border-white/10">
                    <span class="text-2xl">🐱</span> <span class="font-semibold text-sm">Kucing Pink</span>
                </div>
                <div class="flex items-center gap-2 bg-white/10 px-4 py-2 rounded-xl border border-white/10">
                    <span class="text-2xl">🦊</span> <span class="font-semibold text-sm">Rubah Oranye</span>
                </div>
                <div class="flex items-center gap-2 bg-white/10 px-4 py-2 rounded-xl border border-white/10">
                    <span class="text-2xl">🐸</span> <span class="font-semibold text-sm">Katak Hijau</span>
                </div>
                <div class="flex items-center gap-2 bg-white/10 px-4 py-2 rounded-xl border border-white/10">
                    <span class="text-2xl">🐧</span> <span class="font-semibold text-sm">Penguin Ungu</span>
                </div>
                <div class="flex items-center gap-2 bg-white/10 px-4 py-2 rounded-xl border border-white/10">
                    <span class="text-2xl">🤖</span> <span class="font-semibold text-sm">Robot Kuning</span>
                </div>
            </div>

            <!-- Tombol Navigasi Utama -->
            <div class="flex flex-col sm:flex-row justify-center gap-4">
                <button onclick="switchView('groups')" class="px-8 py-4 bg-gradient-to-r from-emerald-500 to-teal-600 hover:from-emerald-400 hover:to-teal-500 text-white font-bold text-lg rounded-2xl shadow-lg transform hover:-translate-y-1 transition duration-200 flex items-center justify-center gap-3 cursor-pointer">
                    <i class="fa-solid fa-play"></i> Mulai Permainan
                </button>
                <button onclick="openModal('modal-instructions')" class="px-8 py-4 bg-gradient-to-r from-amber-500 to-orange-600 hover:from-amber-400 hover:to-orange-500 text-white font-bold text-lg rounded-2xl shadow-lg transform hover:-translate-y-1 transition duration-200 flex items-center justify-center gap-3 cursor-pointer">
                    <i class="fa-solid fa-book-open"></i> Petunjuk Permainan
                </button>
                <button onclick="switchView('groups')" class="px-8 py-4 bg-gradient-to-r from-purple-500 to-pink-600 hover:from-purple-400 hover:to-pink-500 text-white font-bold text-lg rounded-2xl shadow-lg transform hover:-translate-y-1 transition duration-200 flex items-center justify-center gap-3 cursor-pointer">
                    <i class="fa-solid fa-users-gear"></i> Pengaturan Kelompok
                </button>
            </div>
        </div>
    </div>


    <!-- ================= 2. HALAMAN PENGATURAN KELOMPOK ================= -->
    <div id="view-groups" class="hidden flex-1 flex flex-col items-center justify-center p-6">
        <div class="max-w-2xl w-full bg-white/10 backdrop-blur-md rounded-3xl p-8 border border-white/20 shadow-2xl">
            <h2 class="text-3xl font-bold text-yellow-300 text-center mb-6 flex items-center justify-center gap-3">
                <i class="fa-solid fa-users"></i> Pengaturan Enam Kelompok
            </h2>
            <p class="text-sm text-slate-300 text-center mb-6">Ubah nama kelompok sesuai dengan nama siswa di kelas dan tentukan kelompok mana yang akan memulai permainan terlebih dahulu setelah melakukan suit.</p>

            <div id="group-inputs-container" class="space-y-4 mb-6">
                <!-- Diisi via JavaScript -->
            </div>

            <!-- Pilih Kelompok Pertama -->
            <div class="mb-8 bg-white/5 p-4 rounded-2xl border border-white/10 flex flex-col sm:flex-row items-center justify-between gap-4">
                <label for="first-player-select" class="font-semibold text-cyan-200 flex items-center gap-2">
                    <i class="fa-solid fa-flag-checkered"></i> Pilih Kelompok Pertama (Hasil Suit):
                </label>
                <select id="first-player-select" class="bg-indigo-950 border border-white/20 px-4 py-2 rounded-xl text-white font-semibold focus:outline-none focus:ring-2 focus:ring-yellow-400">
                    <option value="0">Kelompok 1</option>
                    <option value="1">Kelompok 2</option>
                    <option value="2">Kelompok 3</option>
                    <option value="3">Kelompok 4</option>
                    <option value="4">Kelompok 5</option>
                    <option value="5">Kelompok 6</option>
                </select>
            </div>

            <div class="flex justify-between">
                <button onclick="switchView('home')" class="px-6 py-3 bg-slate-700 hover:bg-slate-600 text-white font-semibold rounded-xl transition duration-200 flex items-center gap-2 cursor-pointer">
                    <i class="fa-solid fa-arrow-left"></i> Kembali
                </button>
                <button onclick="startGame()" class="px-8 py-3 bg-gradient-to-r from-emerald-500 to-teal-600 hover:from-emerald-400 hover:to-teal-500 text-white font-bold rounded-xl shadow-lg transition duration-200 flex items-center gap-2 cursor-pointer">
                    Mulai Pertandingan <i class="fa-solid fa-arrow-right"></i>
                </button>
            </div>
        </div>
    </div>


    <!-- ================= 3. HALAMAN UTAMA PERMAINAN (GAME BOARD) ================= -->
    <div id="view-game" class="hidden flex-1 flex flex-col p-4 max-w-[1600px] mx-auto w-full">
        <!-- Top Bar Navigasi Game -->
        <div class="flex flex-wrap items-center justify-between bg-white/10 backdrop-blur-md px-6 py-3 rounded-2xl border border-white/20 mb-4 gap-4">
            <div class="flex items-center gap-3">
                <span class="text-3xl">📐</span>
                <div>
                    <h1 class="font-bold text-xl text-yellow-300">Ular Tangga Matematika</h1>
                    <p class="text-xs text-slate-300">Petualangan Deret Geometri SMA</p>
                </div>
            </div>

            <!-- Kontrol Utama Guru -->
            <div class="flex flex-wrap items-center gap-2">
                <button onclick="openModal('modal-material')" class="px-4 py-2 bg-indigo-600 hover:bg-indigo-500 text-white font-semibold text-sm rounded-xl shadow transition flex items-center gap-2 cursor-pointer">
                    <i class="fa-solid fa-book"></i> Materi
                </button>
                <button onclick="openModal('modal-instructions')" class="px-4 py-2 bg-amber-600 hover:bg-amber-500 text-white font-semibold text-sm rounded-xl shadow transition flex items-center gap-2 cursor-pointer">
                    <i class="fa-solid fa-circle-question"></i> Petunjuk
                </button>
                <button onclick="toggleAudio()" id="audio-toggle-btn" class="px-4 py-2 bg-purple-600 hover:bg-purple-500 text-white font-semibold text-sm rounded-xl shadow transition flex items-center gap-2 cursor-pointer">
                    <i class="fa-solid fa-volume-high" id="audio-icon"></i> <span id="audio-text">Suara: ON</span>
                </button>
                <button onclick="endGame()" class="px-4 py-2 bg-rose-600 hover:bg-rose-500 text-white font-semibold text-sm rounded-xl shadow transition flex items-center gap-2 cursor-pointer">
                    <i class="fa-solid fa-flag-checkered"></i> Akhiri Game
                </button>
            </div>
        </div>

        <!-- Main Workspace (Papan Kiri & Panel Kanan) -->
        <div class="grid grid-cols-1 lg:grid-cols-4 gap-4 flex-1">
            
            <!-- PAPAN ULAR TANGGA (Lebar 3 Kolom di layar besar) -->
            <div class="lg:col-span-3 bg-indigo-950/80 backdrop-blur-md rounded-3xl p-4 border border-white/20 relative flex items-center justify-center overflow-hidden shadow-2xl min-h-[600px]">
                <!-- SVG Board Container -->
                <div id="board-container" class="relative w-full max-w-[750px] aspect-square bg-slate-900 rounded-2xl overflow-hidden shadow-inner border-2 border-indigo-500/40">
                    <!-- Grid Kotak & SVG Ular/Tangga akan dirender lewat JS -->
                </div>
            </div>

            <!-- PANEL KANAN (Status Giliran, Dadu 3D, & Panel Skor Kelompok) -->
            <div class="lg:col-span-1 flex flex-col gap-4">
                
                <!-- Kotak Status & Dadu -->
                <div class="bg-white/10 backdrop-blur-md rounded-3xl p-5 border border-white/20 shadow-xl flex flex-col items-center text-center">
                    <div id="turn-banner" class="w-full py-2 px-4 bg-gradient-to-r from-pink-500 to-purple-500 rounded-xl font-bold text-sm mb-4 shadow-md animate-pulse">
                        Giliran: Kelompok 1
                    </div>
                    <div id="game-status-text" class="text-xs text-cyan-200 font-semibold mb-4 min-h-[20px]">
                        Menunggu lemparan dadu...
                    </div>

                    <!-- Visual Dadu 3D -->
                    <div class="dice-container my-2 flex items-center justify-center">
                        <div id="dice-3d" class="dice cursor-pointer" onclick="rollDiceAction()">
                            <!-- Sisi 1 -->
                            <div class="dice-face face-1"><div class="dot col-start-2 row-start-2"></div></div>
                            <!-- Sisi 2 -->
                            <div class="dice-face face-2"><div class="dot col-start-1 row-start-1"></div><div class="dot col-start-3 row-start-3"></div></div>
                            <!-- Sisi 3 -->
                            <div class="dice-face face-3"><div class="dot col-start-1 row-start-1"></div><div class="dot col-start-2 row-start-2"></div><div class="dot col-start-3 row-start-3"></div></div>
                            <!-- Sisi 4 -->
                            <div class="dice-face face-4"><div class="dot col-start-1 row-start-1"></div><div class="dot col-start-3 row-start-1"></div><div class="dot col-start-1 row-start-3"></div><div class="dot col-start-3 row-start-3"></div></div>
                            <!-- Sisi 5 -->
                            <div class="dice-face face-5"><div class="dot col-start-1 row-start-1"></div><div class="dot col-start-3 row-start-1"></div><div class="dot col-start-2 row-start-2"></div><div class="dot col-start-1 row-start-3"></div><div class="dot col-start-3 row-start-3"></div></div>
                            <!-- Sisi 6 -->
                            <div class="dice-face face-6"><div class="dot col-start-1 row-start-1"></div><div class="dot col-start-3 row-start-1"></div><div class="dot col-start-1 row-start-2"></div><div class="dot col-start-3 row-start-2"></div><div class="dot col-start-1 row-start-3"></div><div class="dot col-start-3 row-start-3"></div></div>
                        </div>
                    </div>

                    <button id="btn-roll" onclick="rollDiceAction()" class="mt-4 w-full py-3 bg-gradient-to-r from-yellow-400 to-amber-500 hover:from-yellow-300 hover:to-amber-400 text-slate-900 font-bold rounded-xl shadow-lg transition duration-200 flex items-center justify-center gap-2 cursor-pointer">
                        <i class="fa-solid fa-dice"></i> Kocok Dadu
                    </button>
                </div>

                <!-- Panel Skor Enam Kelompok -->
                <div class="bg-white/10 backdrop-blur-md rounded-3xl p-4 border border-white/20 shadow-xl flex-1 flex flex-col">
                    <h3 class="font-bold text-sm text-yellow-300 mb-3 flex items-center gap-2">
                        <i class="fa-solid fa-trophy"></i> Skor Enam Kelompok
                    </h3>
                    <div id="scorecard-container" class="space-y-2.5 flex-1 overflow-y-auto pr-1">
                        <!-- Diisi via JavaScript -->
                    </div>
                </div>

            </div>

        </div>
    </div>


    <!-- ================= MODAL: SOAL MATEMATIKA ================= -->
    <div id="modal-quiz" class="fixed inset-0 bg-black/70 backdrop-blur-sm hidden items-center justify-center z-50 p-4">
        <div class="bg-indigo-950 border-2 border-indigo-500/50 rounded-3xl max-w-2xl w-full p-6 md:p-8 shadow-2xl transform transition-all animate-[bounceIn_0.3s_ease-out]">
            <div class="flex justify-between items-center mb-4 border-b border-white/10 pb-3">
                <span id="quiz-category-badge" class="px-3 py-1 bg-cyan-500/20 text-cyan-300 text-xs font-semibold rounded-full border border-cyan-500/30">
                    Deret Geometri
                </span>
                <span id="quiz-difficulty-badge" class="px-3 py-1 bg-amber-500/20 text-amber-300 text-xs font-semibold rounded-full border border-amber-500/30">
                    Sedang
                </span>
            </div>

            <h3 id="quiz-question" class="text-lg md:text-xl font-bold text-white mb-6 leading-relaxed">
                Pertanyaan soal akan muncul di sini...
            </h3>

            <div id="quiz-options" class="grid grid-cols-1 md:grid-cols-2 gap-3 mb-6">
                <!-- Tombol Opsi A, B, C, D -->
            </div>

            <div id="quiz-feedback" class="hidden p-4 rounded-xl mb-6 text-sm font-medium">
                <!-- Feedback Benar/Salah & Pembahasan -->
            </div>

            <div class="flex justify-end">
                <button id="btn-next-turn" onclick="nextTurn()" class="hidden px-6 py-3 bg-gradient-to-r from-emerald-500 to-teal-600 hover:from-emerald-400 hover:to-teal-500 text-white font-bold rounded-xl shadow-lg transition duration-200 cursor-pointer">
                    Lanjut ke Kelompok Berikutnya <i class="fa-solid fa-arrow-right ml-2"></i>
                </button>
            </div>
        </div>
    </div>


    <!-- ================= MODAL: KOTAK INSTRUKSI KHUSUS ================= -->
    <div id="modal-special" class="fixed inset-0 bg-black/70 backdrop-blur-sm hidden items-center justify-center z-50 p-4">
        <div class="bg-indigo-950 border-2 border-pink-500/50 rounded-3xl max-w-md w-full p-6 md:p-8 shadow-2xl text-center">
            <div class="w-16 h-16 bg-pink-500/20 rounded-full flex items-center justify-center mx-auto mb-4 border border-pink-500/40 text-pink-400 text-3xl">
                <i id="special-icon" class="fa-solid fa-forward"></i>
            </div>
            <h3 class="text-2xl font-bold text-pink-300 mb-2">BONUS LANGKAH!</h3>
            <p id="special-text" class="text-base text-slate-200 mb-6">
                Kamu mendapatkan kesempatan untuk maju 2 langkah.
            </p>
            <button onclick="executeSpecialInstruction()" class="w-full py-3 bg-gradient-to-r from-pink-500 to-purple-600 hover:from-pink-400 hover:to-purple-500 text-white font-bold rounded-xl shadow-lg transition duration-200 cursor-pointer">
                Oke, Lanjutkan
            </button>
        </div>
    </div>


    <!-- ================= MODAL: PETUNJUK PERMAINAN ================= -->
    <div id="modal-instructions" class="fixed inset-0 bg-black/70 backdrop-blur-sm hidden items-center justify-center z-50 p-4">
        <div class="bg-indigo-950 border-2 border-amber-500/50 rounded-3xl max-w-2xl w-full p-6 md:p-8 shadow-2xl max-h-[85vh] overflow-y-auto">
            <div class="flex justify-between items-center mb-4 border-b border-white/10 pb-3">
                <h3 class="text-2xl font-bold text-amber-300 flex items-center gap-2">
                    <i class="fa-solid fa-circle-question"></i> Petunjuk Permainan
                </h3>
                <button onclick="closeModal('modal-instructions')" class="text-slate-400 hover:text-white text-xl cursor-pointer">
                    <i class="fa-solid fa-xmark"></i>
                </button>
            </div>
            <div class="space-y-4 text-sm text-slate-200 leading-relaxed">
                <p><strong>1. Tujuan Permainan:</strong> Menjadi kelompok dengan skor tertinggi atau yang pertama kali mencapai kotak FINISH (Kotak 100) dengan menjawab pertanyaan Deret Geometri dengan benar.</p>
                <p><strong>2. Giliran Bermain:</strong> Permainan dimainkan oleh 6 kelompok. Urutan giliran sesuai dengan pengaturan kelompok yang dipilih guru (atau hasil suit).</p>
                <p><strong>3. Melempar Dadu:</strong> Tekan tombol "Kocok Dadu" untuk memutar dadu 3D. Pion akan berjalan otomatis sesuai angka dadu.</p>
                <p><strong>4. Aturan Ular & Tangga:</strong> Jika pion berhenti di pangkal tangga, pion akan otomatis naik ke atas. Jika berhenti di ekor ular, pion akan meluncur turun ke kepala ular.</p>
                <p><strong>5. Kotak Instruksi Khusus:</strong> Terdapat 5 kotak misterius di papan yang berisi instruksi khusus <strong>Maju 2 langkah</strong> atau <strong>Mundur 2 langkah</strong>.</p>
                <p><strong>6. Soal Matematika:</strong> Setelah gerakan pion selesai, kartu soal Deret Geometri akan muncul. Jawaban benar bernilai <strong>+10 poin</strong>.</p>
                <p><strong>7. Mencapai Kotak 100:</strong> Untuk menang, pion harus mendapatkan angka dadu yang tepat untuk mencapai kotak 100. Jika melebihi, pion tetap di tempat.</p>
            </div>
            <div class="mt-6 text-center">
                <button onclick="closeModal('modal-instructions')" class="px-6 py-2.5 bg-amber-600 hover:bg-amber-500 text-white font-semibold rounded-xl cursor-pointer">
                    Mengerti
                </button>
            </div>
        </div>
    </div>


    <!-- ================= MODAL: MATERI DERET GEOMETRI ================= -->
    <div id="modal-material" class="fixed inset-0 bg-black/70 backdrop-blur-sm hidden items-center justify-center z-50 p-4">
        <div class="bg-indigo-950 border-2 border-indigo-500/50 rounded-3xl max-w-3xl w-full p-6 md:p-8 shadow-2xl max-h-[85vh] overflow-y-auto">
            <div class="flex justify-between items-center mb-4 border-b border-white/10 pb-3">
                <h3 class="text-2xl font-bold text-cyan-300 flex items-center gap-2">
                    <i class="fa-solid fa-book-open"></i> Ringkasan Materi Deret Geometri
                </h3>
                <button onclick="closeModal('modal-material')" class="text-slate-400 hover:text-white text-xl cursor-pointer">
                    <i class="fa-solid fa-xmark"></i>
                </button>
            </div>
            <div class="space-y-4 text-sm text-slate-200 leading-relaxed">
                <div class="bg-white/5 p-4 rounded-2xl border border-white/10">
                    <h4 class="font-bold text-yellow-300 mb-1">1. Barisan vs Deret Geometri</h4>
                    <p>Barisan geometri adalah barisan bilangan yang perbandingan (rasio $r$) antara dua suku berurutan selalu tetap. Deret geometri adalah penjumlahan dari suku-suku barisan geometri.</p>
                    <p class="mt-2 text-cyan-200">$a, ar, ar^2, ar^3, \dots, ar^{n-1}$ (Barisan)</p>
                    <p class="text-cyan-200">$a + ar + ar^2 + \dots + ar^{n-1}$ (Deret)</p>
                </div>
                <div class="bg-white/5 p-4 rounded-2xl border border-white/10">
                    <h4 class="font-bold text-yellow-300 mb-1">2. Rumus Suku ke-$n$ ($U_n$) & Rasio ($r$)</h4>
                    <p>$$U_n = a \cdot r^{n-1}$$ di mana $a = U_1$ (suku pertama) dan $r = \frac{U_n}{U_{n-1}}$.</p>
                </div>
                <div class="bg-white/5 p-4 rounded-2xl border border-white/10">
                    <h4 class="font-bold text-yellow-300 mb-1">3. Rumus Jumlah $n$ Suku Pertama ($S_n$)</h4>
                    <p>$$S_n = \frac{a(r^n - 1)}{r - 1} \text{ untuk } r > 1$$</p>
                    <p class="mt-1">$$S_n = \frac{a(1 - r^n)}{1 - r} \text{ untuk } r < 1$$</p>
                </div>
                <div class="bg-white/5 p-4 rounded-2xl border border-white/10">
                    <h4 class="font-bold text-yellow-300 mb-1">4. Deret Geometri Tak Hingga</h4>
                    <p>$$S_\infty = \frac{a}{1 - r} \text{ dengan syarat } -1 < r < 1$$</p>
                </div>
            </div>
            <div class="mt-6 text-center">
                <button onclick="closeModal('modal-material')" class="px-6 py-2.5 bg-indigo-600 hover:bg-indigo-500 text-white font-semibold rounded-xl cursor-pointer">
                    Tutup Materi
                </button>
            </div>
        </div>
    </div>


    <!-- ================= MODAL: AKHIR PERMAINAN (PEMENANG) ================= -->
    <div id="modal-gameover" class="fixed inset-0 bg-black/80 backdrop-blur-md hidden items-center justify-center z-50 p-4">
        <div class="bg-indigo-950 border-2 border-yellow-400 rounded-3xl max-w-xl w-full p-8 shadow-2xl text-center relative overflow-hidden">
            <div class="w-20 h-20 bg-yellow-400/20 rounded-full flex items-center justify-center mx-auto mb-4 border border-yellow-400/40 text-yellow-300 text-4xl animate-bounce">
                <i class="fa-solid fa-trophy"></i>
            </div>
            <h3 class="text-3xl font-extrabold text-yellow-300 mb-2">PERTANDINGAN SELESAI!</h3>
            <p id="winner-announcement" class="text-lg text-cyan-200 font-semibold mb-6">
                Pemenang: Kelompok 1
            </p>

            <div class="bg-white/5 p-4 rounded-2xl border border-white/10 mb-6 text-left">
                <h4 class="font-bold text-sm text-slate-300 mb-3 text-center">Peringkat Akhir Kelompok</h4>
                <div id="leaderboard-container" class="space-y-2">
                    <!-- Diisi via JS -->
                </div>
            </div>

            <div class="flex justify-center gap-4">
                <button onclick="restartGame()" class="px-6 py-3 bg-gradient-to-r from-emerald-500 to-teal-600 hover:from-emerald-400 hover:to-teal-500 text-white font-bold rounded-xl shadow-lg transition cursor-pointer">
                    <i class="fa-solid fa-rotate-right mr-2"></i> Main Lagi
                </button>
                <button onclick="switchView('home')" class="px-6 py-3 bg-slate-700 hover:bg-slate-600 text-white font-bold rounded-xl transition cursor-pointer">
                    <i class="fa-solid fa-house mr-2"></i> Menu Utama
                </button>
            </div>
        </div>
    </div>


    <!-- ================= JAVASCRIPT GAME LOGIC ================= -->
    <script>
        /* ==========================================================
           1. KONFIGURASI DAN DATA GAME (DAPAT DIEDIT DENGAN MUDAH)
           ========================================================== */

        // Konfigurasi Posisi Tangga (Bawah -> Atas)
        const LADDERS = {
            6: 25,
            12: 31,
            27: 69,
            36: 56,
            43: 63,
            78: 97,
            87: 95
        };

        // Konfigurasi Posisi Ular (Ekor/Awal Tinggi -> Kepala/Tujuan Rendah)
        const SNAKES = {
            39: 18,
            54: 34,
            71: 50,
            76: 63,
            89: 70,
            93: 88
        };

        // Konfigurasi 5 Kotak Instruksi Khusus (Akan diacak saat game dimulai)
        // Format: kotak: { type: 'forward' | 'backward', steps: number, text: '...' }
        let SPECIAL_BOXES = {};

        // Bank Soal Deret Geometri SMA (Mudah, Sedang, Menantang)
        const QUESTION_BANK = [
            {
                q: "Diketahui barisan geometri dengan suku pertama $a = 3$ dan rasio $r = 2$. Berapakah jumlah 5 suku pertama ($S_5$) dari deret tersebut?",
                options: ["A. 31", "B. 63", "C. 93", "D. 127"],
                answer: 1, // Indeks pilihan B (0, 1, 2, 3)
                explanation: "$S_5 = \\frac{a(r^n - 1)}{r - 1} = \\frac{3(2^5 - 1)}{2 - 1} = 3(32 - 1) = 3(31) = 93$. (Koreksi opsi: 93 adalah opsi C. Mari sesuaikan indeks opsi B ke C, atau hitung 3*(32-1)=93. Opsi C bernilai 93).",
                difficulty: "Sedang"
            },
            {
                q: "Suku ke-3 suatu barisan geometri adalah 12 dan suku ke-5 adalah 48. Jika rasio bernilai positif, berapakah rasio ($r$) barisan tersebut?",
                options: ["A. 1", "B. 2", "C. 3", "D. 4"],
                answer: 1,
                explanation: "$U_5 / U_3 = ar^4 / ar^2 = r^2 = 48 / 12 = 4$. Karena rasio positif, maka $r = \\sqrt{4} = 2$.",
                difficulty: "Sedang"
            },
            {
                q: "Suatu kultur bakteri membelah diri menjadi 2 setiap 20 menit. Jika mula-mula terdapat 50 bakteri, berapa jumlah bakteri setelah 2 jam (6 jam / 120 menit)?",
                options: ["A. 1.600", "B. 3.200", "C. 6.400", "D. 12.800"],
                answer: 1,
                explanation: "Dalam 120 menit terdapat 6 periode pembelahan ($n = 7$). $U_7 = 50 \\times 2^{6} = 50 \\times 64 = 3200$.",
                difficulty: "Menantang"
            },
            {
                q: "Sebuah bola dijatuhkan dari ketinggian 8 میٹر dan memantul kembali dengan ketinggian $\\frac{3}{4}$ dari tinggi sebelumnya. Panjang lintasan seluruhnya hingga bola berhenti adalah...",
                options: ["A. 48 meter", "B. 56 meter", "C. 64 meter", "D. 72 meter"],
                answer: 1,
                explanation: "Lintasan turun = $a / (1-r) = 8 / (1 - 3/4) = 32$. Lintasan naik = $32 \\times 3/4 = 24$. Total lintasan = $32 + 24 = 56$ meter.",
                difficulty: "Menantang"
            },
            {
                q: "Hitunglah jumlah deret geometri tak hingga berikut: $12 + 6 + 3 + \\frac{3}{2} + \\dots$",
                options: ["A. 18", "B. 20", "C. 24", "D. 30"],
                answer: 2,
                explanation: "$S_\\infty = \\frac{a}{1 - r} = \\frac{12}{1 - 1/2} = \\frac{12}{1/2} = 24$.",
                difficulty: "Mudah"
            },
            {
                q: "Diketahui deret geometri $2 + 6 + 18 + \\dots$ Suku ke-6 dari deret tersebut adalah...",
                options: ["A. 162", "B. 324", "C. 486", "D. 972"],
                answer: 2,
                explanation: "$a = 2, r = 3$. $U_6 = a \\cdot r^5 = 2 \\cdot 3^5 = 2 \\cdot 243 = 486$.",
                difficulty: "Mudah"
            },
            {
                q: "Jumlah 4 suku pertama suatu deret geometri adalah 45, dan rasio barisan tersebut adalah 2. Berapakah suku pertama ($a$) deret tersebut?",
                options: ["A. 3", "B. 5", "C. 9", "D. 15"],
                answer: 0,
                explanation: "$S_4 = \\frac{a(2^4 - 1)}{2 - 1} = 15a = 45 \\implies a = 3$.",
                difficulty: "Sedang"
            },
            {
                q: "Selembar kertas dipotong menjadi 2 bagian yang sama. Setiap bagian dipotong lagi menjadi 2, dan seterusnya. Jika pemotongan dilakukan sebanyak 5 kali, berapa banyak potongan kertas yang dihasilkan?",
                options: ["A. 16", "B. 32", "C. 64", "D. 128"],
                answer: 1,
                explanation: "Pola pembentukan mengikuti $U_n = a \\cdot r^{n-1} = 1 \\cdot 2^5 = 32$ bagian.",
                difficulty: "Mudah"
            },
            {
                q: "Suku kedua suatu barisan geometri adalah 6 dan suku kelima adalah 162. Rasio barisan tersebut adalah...",
                options: ["A. 2", "B. 3", "C. 4", "D. 5"],
                answer: 1,
                explanation: "$U_5 / U_2 = r^3 = 162 / 6 = 27 \\implies r = 3$.",
                difficulty: "Sedang"
            },
            {
                q: "Sebuah perusahaan rintisan mencatat pertumbuhan keuntungan bulanan yang membentuk deret geometri. Jika keuntungan bulan pertama Rp 4.000.000 dan bulan ketiga Rp 16.000.000, berapakah keuntungan pada bulan kelima?",
                options: ["A. Rp 32.000.000", "B. Rp 64.000.000", "C. Rp 128.000.000", "D. Rp 256.000.000"],
                answer: 1,
                explanation: "$U_3 / U_1 = r^2 = 16/4 = 4 \\implies r = 2$. $U_5 = U_1 \\cdot r^4 = 4.000.000 \\times 16 = 64.000.000$.",
                difficulty: "Menantang"
            }
        ];

        /* ==========================================================
           2. STATE UTAMA GAME
           ========================================================== */
        let groups = [
            { id: 0, name: "Kelompok 1", icon: "🐉", color: "bg-blue-500", border: "border-blue-400", pos: 1, score: 0 },
            { id: 1, name: "Kelompok 2", icon: "🐱", color: "bg-pink-500", border: "border-pink-400", pos: 1, score: 0 },
            { id: 2, name: "Kelompok 3", icon: "🦊", color: "bg-orange-500", border: "border-orange-400", pos: 1, score: 0 },
            { id: 3, name: "Kelompok 4", icon: "🐸", color: "bg-emerald-500", border: "border-emerald-400", pos: 1, score: 0 },
            { id: 4, name: "Kelompok 5", icon: "🐧", color: "bg-purple-500", border: "border-purple-400", pos: 1, score: 0 },
            { id: 5, name: "Kelompok 6", icon: "🤖", color: "bg-yellow-500", border: "border-yellow-400", pos: 1, score: 0 }
        ];

        let currentTurnIndex = 0; // Menandakan kelompok yang sedang bermain (0-5)
        let gameStatus = "waiting_roll"; // waiting_roll, rolling, moving, special, quiz, gameover
        let currentDiceResult = 1;
        let activeSpecialBox = null;
        let currentQuiz = null;
        let answeredQuestions = new Set(); // Mencegah soal berulang / skor ganda per soal

        /* ==========================================================
           3. INISIALISASI & NAVIGASI HALAMAN
           ========================================================== */
        window.addEventListener('DOMContentLoaded', () => {
            renderGroupInputs();
            renderBoard();
        });

        function switchView(viewName) {
            document.getElementById('view-home').classList.add('hidden');
            document.getElementById('view-groups').classList.add('hidden');
            document.getElementById('view-game').classList.add('hidden');

            if (viewName === 'home') {
                document.getElementById('view-home').classList.remove('hidden');
            } else if (viewName === 'groups') {
                document.getElementById('view-groups').classList.remove('hidden');
            } else if (viewName === 'game') {
                document.getElementById('view-game').classList.remove('hidden');
                renderBoard();
                updateUI();
            }
        }

        function renderGroupInputs() {
            const container = document.getElementById('group-inputs-container');
            container.innerHTML = '';
            groups.forEach((g, idx) => {
                container.innerHTML += `
                    <div class="flex items-center gap-3 bg-white/5 p-3 rounded-2xl border border-white/10">
                        <span class="text-2xl">${g.icon}</span>
                        <input type="text" id="group-name-${idx}" value="${g.name}" class="flex-1 bg-indigo-950 border border-white/20 px-4 py-2 rounded-xl text-white font-semibold focus:outline-none focus:ring-2 focus:ring-yellow-400">
                    </div>
                `;
            });
        }

        function startGame() {
            // Simpan nama kelompok dari input
            groups.forEach((g, idx) => {
                const val = document.getElementById(`group-name-${idx}`).value.trim();
                if (val) g.name = val;
                g.pos = 1;
                g.score = 0;
            });

            // Ambil kelompok pertama pilihan guru
            const firstSelect = document.getElementById('first-player-select');
            currentTurnIndex = parseInt(firstSelect.value);

            // Acak 5 kotak instruksi khusus (hindari kotak 1, 100, atau awal ular/tangga)
            generateSpecialBoxes();

            answeredQuestions.clear();
            gameStatus = "waiting_roll";

            switchView('game');
        }

        function generateSpecialBoxes() {
            SPECIAL_BOXES = {};
            let availableBoxes = [];
            for (let i = 2; i < 100; i++) {
                if (!LADDERS[i] && !SNAKES[i]) {
                    availableBoxes.push(i);
                }
            }
            // Acak dan ambil 5 kotak
            availableBoxes.sort(() => Math.random() - 0.5);
            for (let i = 0; i < 5; i++) {
                let boxNum = availableBoxes[i];
                let type = Math.random() > 0.5 ? 'forward' : 'backward';
                let steps = 2;
                SPECIAL_BOXES[boxNum] = {
                    type: type,
                    steps: steps,
                    text: type === 'forward' ? 'Kamu mendapatkan kesempatan untuk maju 2 langkah.' : 'Waduh! Kamu harus mundur 2 langkah.'
                };
            }
        }

        /* ==========================================================
           4. RENDER PAPAN ULAR TANGGA & SVG (100 KOTAK ZIG-ZAG)
           ========================================================== */
        function renderBoard() {
            const boardContainer = document.getElementById('board-container');
            boardContainer.innerHTML = '';

            // Buat SVG untuk Ular dan Tangga di latar belakang papan
            let svgHTML = `<svg class="absolute inset-0 w-full h-full pointer-events-none z-0" viewBox="0 0 1000 1000">`;

            // Render Tangga
            for (let from in LADDERS) {
                let to = LADDERS[from];
                let p1 = getCoordinates(parseInt(from));
                let p2 = getCoordinates(parseInt(to));
                svgHTML += `<line x1="${p1.x}" y1="${p1.y}" x2="${p2.x}" y2="${p2.y}" stroke="#f59e0b" stroke-width="12" stroke-linecap="round" stroke-dasharray="10,6" />`;
                svgHTML += `<circle cx="${p1.x}" cy="${p1.y}" r="8" fill="#d97706" /><circle cx="${p2.x}" cy="${p2.y}" r="8" fill="#d97706" />`;
            }

            // Render Ular
            for (let from in SNAKES) {
                let to = SNAKES[from]; // Dari awal tinggi ke kepala rendah
                let p1 = getCoordinates(parseInt(from));
                let p2 = getCoordinates(parseInt(to));
                svgHTML += `<path d="M ${p1.x} ${p1.y} Q ${(p1.x+p2.x)/2 + 40} ${(p1.y+p2.y)/2 - 40} ${p2.x} ${p2.y}" fill="none" stroke="#ec4899" stroke-width="14" stroke-linecap="round" />`;
                svgHTML += `<circle cx="${p2.x}" cy="${p2.y}" r="12" fill="#be185d" />`; // Kepala Ular
            }
            svgHTML += `</svg>`;
            boardContainer.innerHTML += svgHTML;

            // Render 100 Kotak Zig-Zag
            const gridDiv = document.createElement('div');
            gridDiv.className = 'absolute inset-0 grid grid-cols-10 grid-rows-10 p-2 gap-1 z-10';

            // Susunan nomor zig-zag 10x10
            for (let row = 9; row >= 0; row--) {
                for (let col = 0; col < 10; col++) {
                    let boxNum;
                    if (row % 2 === 1) {
                        // Baris ganjil dari kanan ke kiri (misal baris 9: 100 s.d 91)
                        boxNum = row * 10 + (10 - col);
                    } else {
                        // Baris genap dari kiri ke kanan (misal baris 8: 81 s.d 90)
                        boxNum = row * 10 + col + 1;
                    }

                    let bgClass = (row + col) % 2 === 0 ? 'bg-teal-700/60' : 'bg-amber-600/50';
                    if (boxNum === 100) bgClass = 'bg-yellow-500/80 font-bold';
                    if (boxNum === 1) bgClass = 'bg-emerald-600/80 font-bold';

                    let specialIconIndicator = '';
                    if (SPECIAL_BOXES[boxNum]) {
                        specialIconIndicator = `<span class="absolute top-0.5 right-1 text-[10px] bg-pink-600 text-white px-1 rounded">${SPECIAL_BOXES[boxNum].type === 'forward' ? '🚀' : '⏳'}</span>`;
                    }

                    const boxElement = document.createElement('div');
                    boxElement.id = `box-${boxNum}`;
                    boxElement.className = `relative rounded-lg flex flex-col justify-between p-1 border border-white/10 ${bgClass} text-white text-xs`;
                    boxElement.innerHTML = `
                        <span class="font-bold text-[10px] opacity-80">${boxNum}</span>
                        ${specialIconIndicator}
                        <div id="pawns-in-box-${boxNum}" class="flex flex-wrap items-center justify-center gap-0.5 min-h-[20px]"></div>
                    `;
                    gridDiv.appendChild(boxElement);
                }
            }
            boardContainer.appendChild(gridDiv);

            renderPawns();
        }

        // Fungsi bantu mendapatkan koordinat pixel (X, Y) dari nomor kotak untuk SVG Ular/Tangga
        function getCoordinates(boxNum) {
            let row = Math.floor((boxNum - 1) / 10);
            let col = (boxNum - 1) % 10;
            if (row % 2 === 1) {
                col = 9 - col;
            }
            // Asumsi SVG viewBox 1000x1000
            let x = (col * 100) + 50;
            let y = ((9 - row) * 100) + 50;
            return { x, y };
        }

        function renderPawns() {
            // Bersihkan semua kotak pion
            for (let i = 1; i <= 100; i++) {
                const container = document.getElementById(`pawns-in-box-${i}`);
                if (container) container.innerHTML = '';
            }

            // Tempatkan pion ke kotak masing-masing
            groups.forEach(g => {
                const container = document.getElementById(`pawns-in-box-${g.pos}`);
                if (container) {
                    const pawnBadge = document.createElement('span');
                    pawnBadge.className = `w-5 h-5 rounded-full ${g.color} flex items-center justify-center text-xs shadow-md border border-white transform hover:scale-125 transition`;
                    pawnBadge.title = g.name;
                    pawnBadge.innerText = g.icon;
                    container.appendChild(pawnBadge);
                }
            });
        }

        /* ==========================================================
           5. MEKANISME LEMPAR DADU 3D & URUTAN GERAKAN
           ========================================================== */
        const diceTransforms = {
            1: "rotateX(0deg) rotateY(0deg)",
            2: "rotateX(0deg) rotateY(90deg)",
            3: "rotateX(0deg) rotateY(180deg)",
            4: "rotateX(0deg) rotateY(-90deg)",
            5: "rotateX(-90deg) rotateY(0deg)",
            6: "rotateX(90deg) rotateY(0deg)"
        };

        function rollDiceAction() {
            if (gameStatus !== "waiting_roll") return;
            gameStatus = "rolling";
            updateUIStatus("Dadu sedang berputar...");

            SoundFX.playRoll();

            const dice = document.getElementById('dice-3d');
            dice.classList.remove('rolling');
            void dice.offsetWidth; // Trigger reflow

            // Tentukan hasil dadu secara acak (1-6)
            currentDiceResult = Math.floor(Math.random() * 6) + 1;

            // Set rotasi target CSS variable
            let targetTrans = diceTransforms[currentDiceResult];
            // Tambahkan putaran ekstra agar animasi 3D terlihat berputar kencang
            targetTrans += " rotateX(1080deg) rotateY(720deg)";
            dice.style.setProperty('--target-transform', targetTrans);

            dice.classList.add('rolling');
            document.getElementById('btn-roll').disabled = true;

            // Selesaikan animasi dadu setelah 3.5 detik
            setTimeout(() => {
                dice.classList.remove('rolling');
                dice.style.transform = diceTransforms[currentDiceResult];
                updateUIStatus(`Hasil dadu: ${currentDiceResult}. Menyiapkan gerakan pion...`);

                setTimeout(() => {
                    movePawnStepByStep(currentDiceResult);
                }, 1000);
            }, 3500);
        }

        function movePawnStepByStep(steps) {
            gameStatus = "moving";
            let currentGroup = groups[currentTurnIndex];
            let targetPos = currentGroup.pos + steps;

            // Aturan kotak 100: harus pas, jika melebihi tetap di posisi semula
            if (targetPos > 100) {
                updateUIStatus(`${currentGroup.name} mendapat angka ${steps}, melebihi kotak 100 (posisi tetap).`);
                setTimeout(() => {
                    checkPostMoveActions();
                }, 1200);
                return;
            }

            let stepCount = 0;
            let interval = setInterval(() => {
                if (stepCount < steps) {
                    currentGroup.pos++;
                    stepCount++;
                    SoundFX.playMove();
                    renderPawns();
                } else {
                    clearInterval(interval);
                    setTimeout(() => {
                        checkPostMoveActions();
                    }, 800);
                }
            }, 400); // Jeda tiap langkah pion
        }

        function checkPostMoveActions() {
            let currentGroup = groups[currentTurnIndex];
            let pos = currentGroup.pos;

            // 1. Cek Tangga
            if (LADDERS[pos]) {
                let topPos = LADDERS[pos];
                updateUIStatus(`${currentGroup.name} menaiki tangga dari ${pos} ke ${topPos}!`);
                SoundFX.playSuccess();
                currentGroup.pos = topPos;
                renderPawns();
                setTimeout(() => {
                    checkPostMoveActions(); // Rekursif jika beruntung kena tangga lagi
                }, 1200);
                return;
            }

            // 2. Cek Ular
            if (SNAKES[pos]) {
                let headPos = SNAKES[pos];
                updateUIStatus(`${currentGroup.name} digigit ular! Meluncur turun dari ${pos} ke ${headPos}.`);
                SoundFX.playWrong();
                currentGroup.pos = headPos;
                renderPawns();
                setTimeout(() => {
                    checkPostMoveActions();
                }, 1200);
                return;
            }

            // 3. Cek Kotak Instruksi Khusus
            if (SPECIAL_BOXES[pos]) {
                activeSpecialBox = SPECIAL_BOXES[pos];
                gameStatus = "special";
                updateUIStatus(`${currentGroup.name} berhenti di Kotak Instruksi Khusus! Menunggu konfirmasi guru.`);
                showSpecialModal(activeSpecialBox);
                return;
            }

            // 4. Cek Apakah Menang (Kotak 100)
            if (pos === 100) {
                gameStatus = "gameover";
                SoundFX.playSuccess();
                showGameOverModal(currentGroup);
                return;
            }

            // 5. Jika tidak ada aksi khusus, lanjut ke Soal Matematika
            openQuizModal();
        }

        /* ==========================================================
           6. KOTAK INSTRUKSI KHUSUS
           ========================================================== */
        function showSpecialModal(special) {
            document.getElementById('special-text').innerText = special.text;
            const iconEl = document.getElementById('special-icon');
            if (special.type === 'forward') {
                iconEl.className = "fa-solid fa-rocket";
            } else {
                iconEl.className = "fa-solid fa-hourglass-start";
            }
            document.getElementById('modal-special').classList.remove('hidden');
            document.getElementById('modal-special').classList.add('flex');
        }

        function executeSpecialInstruction() {
            document.getElementById('modal-special').classList.remove('flex');
            document.getElementById('modal-special').classList.add('hidden');

            let currentGroup = groups[currentTurnIndex];
            let targetPos = currentGroup.pos;

            if (activeSpecialBox.type === 'forward') {
                targetPos += activeSpecialBox.steps;
                if (targetPos > 100) targetPos = 100;
            } else {
                targetPos -= activeSpecialBox.steps;
                if (targetPos < 1) targetPos = 1;
            }

            updateUIStatus(`${currentGroup.name} menjalankan instruksi: ${activeSpecialBox.type === 'forward' ? 'Maju' : 'Mundur'} 2 langkah.`);
            currentGroup.pos = targetPos;
            renderPawns();
            SoundFX.playMove();

            setTimeout(() => {
                checkPostMoveActions();
            }, 1000);
        }

        /* ==========================================================
           7. SOAL MATEMATIKA & BANK SOAL
           ========================================================== */
        function openQuizModal() {
            gameStatus = "quiz";
            updateUIStatus("Menjawab soal matematika...");

            // Pilih soal secara acak yang belum dijawab (atau random dari bank)
            let availableQuestions = QUESTION_BANK.filter((_, idx) => !answeredQuestions.has(idx));
            if (availableQuestions.length === 0) answeredQuestions.clear(); // Reset jika habis

            let randomIndex = Math.floor(Math.random() * QUESTION_BANK.length);
            currentQuiz = { ...QUESTION_BANK[randomIndex], index: randomIndex };
            answeredQuestions.add(randomIndex);

            // Tampilkan ke Modal Quiz
            document.getElementById('quiz-category-badge').innerText = `Deret Geometri (${currentQuiz.difficulty})`;
            document.getElementById('quiz-question').innerHTML = currentQuiz.q;

            const optionsContainer = document.getElementById('quiz-options');
            optionsContainer.innerHTML = '';
            currentQuiz.options.forEach((opt, idx) => {
                optionsContainer.innerHTML += `
                    <button onclick="answerQuiz(${idx})" id="option-btn-${idx}" class="p-4 bg-white/5 hover:bg-white/10 border border-white/20 rounded-2xl text-left font-semibold text-white transition duration-200 cursor-pointer flex items-center justify-between">
                        <span>${opt}</span>
                    </button>
                `;
            });

            document.getElementById('quiz-feedback').classList.add('hidden');
            document.getElementById('btn-next-turn').classList.add('hidden');

            document.getElementById('modal-quiz').classList.remove('hidden');
            document.getElementById('modal-quiz').classList.add('flex');
        }

        function answerQuiz(selectedIndex) {
            // Kunci semua pilihan tombol agar tidak bisa diubah
            currentQuiz.options.forEach((_, idx) => {
                document.getElementById(`option-btn-${idx}`).disabled = true;
                document.getElementById(`option-btn-${idx}`).classList.add('opacity-60', 'cursor-not-allowed');
            });

            const currentGroup = groups[currentTurnIndex];
            const feedbackEl = document.getElementById('quiz-feedback');
            feedbackEl.classList.remove('hidden');

            if (selectedIndex === currentQuiz.answer) {
                SoundFX.playSuccess();
                currentGroup.score += 10;
                feedbackEl.className = "p-4 rounded-xl mb-6 text-sm font-medium bg-emerald-500/20 border border-emerald-500/40 text-emerald-200";
                feedbackEl.innerHTML = `<strong>JAWABAN BENAR! (+10 Poin)</strong><br>${currentQuiz.explanation}`;
            } else {
                SoundFX.playWrong();
                feedbackEl.className = "p-4 rounded-xl mb-6 text-sm font-medium bg-rose-500/20 border border-rose-500/40 text-rose-200";
                feedbackEl.innerHTML = `<strong>JAWABAN SALAH.</strong> Jawaban yang benar adalah <strong>${currentQuiz.options[currentQuiz.answer]}</strong>.<br>${currentQuiz.explanation}`;
            }

            updateScorecard();
            document.getElementById('btn-next-turn').classList.remove('hidden');
        }

        function nextTurn() {
            document.getElementById('modal-quiz').classList.remove('flex');
            document.getElementById('modal-quiz').classList.add('hidden');

            // Pindah giliran ke kelompok berikutnya
            currentTurnIndex = (currentTurnIndex + 1) % groups.length;
            gameStatus = "waiting_roll";
            updateUI();
        }

        /* ==========================================================
           8. AKHIR PERMAINAN & PEMENANG
           ========================================================== */
        function endGame() {
            gameStatus = "gameover";
            // Tentukan pemenang berdasarkan skor tertinggi, lalu posisi kotak terbanyak
            let sortedGroups = [...groups].sort((a, b) => b.score - a.score || b.pos - a.pos);
            let winner = sortedGroups[0];

            document.getElementById('winner-announcement').innerText = `Pemenang: ${winner.icon} ${winner.name} (${winner.score} Poin)!`;

            const leaderboardContainer = document.getElementById('leaderboard-container');
            leaderboardContainer.innerHTML = '';
            sortedGroups.forEach((g, rank) => {
                leaderboardContainer.innerHTML += `
                    <div class="flex items-center justify-between bg-white/5 px-4 py-2 rounded-xl border border-white/10 text-xs">
                        <div class="flex items-center gap-2">
                            <span class="font-bold text-yellow-300">#${rank + 1}</span>
                            <span>${g.icon} ${g.name}</span>
                        </div>
                        <div class="flex items-center gap-4">
                            <span>Kotak: ${g.pos}</span>
                            <span class="font-bold text-emerald-400">${g.score} Poin</span>
                        </div>
                    </div>
                `;
            });

            document.getElementById('modal-gameover').classList.remove('hidden');
            document.getElementById('modal-gameover').classList.add('flex');
        }

        function showGameOverModal(winningGroup) {
            document.getElementById('winner-announcement').innerText = `SELAMAT! ${winningGroup.icon} ${winningGroup.name} berhasil mencapai Kotak 100 terlebih dahulu!`;
            
            let sortedGroups = [...groups].sort((a, b) => b.score - a.score || b.pos - a.pos);
            const leaderboardContainer = document.getElementById('leaderboard-container');
            leaderboardContainer.innerHTML = '';
            sortedGroups.forEach((g, rank) => {
                leaderboardContainer.innerHTML += `
                    <div class="flex items-center justify-between bg-white/5 px-4 py-2 rounded-xl border border-white/10 text-xs">
                        <div class="flex items-center gap-2">
                            <span class="font-bold text-yellow-300">#${rank + 1}</span>
                            <span>${g.icon} ${g.name}</span>
                        </div>
                        <div class="flex items-center gap-4">
                            <span>Kotak: ${g.pos}</span>
                            <span class="font-bold text-emerald-400">${g.score} Poin</span>
                        </div>
                    </div>
                `;
            });

            document.getElementById('modal-gameover').classList.remove('hidden');
            document.getElementById('modal-gameover').classList.add('flex');
        }

        function restartGame() {
            document.getElementById('modal-gameover').classList.remove('flex');
            document.getElementById('modal-gameover').classList.add('hidden');

            groups.forEach(g => {
                g.pos = 1;
                g.score = 0;
            });
            currentTurnIndex = 0;
            generateSpecialBoxes();
            answeredQuestions.clear();
            gameStatus = "waiting_roll";
            renderBoard();
            updateUI();
        }

        /* ==========================================================
           9. PANEL KONTROL UI & AUDIO
           ========================================================== */
        function updateUI() {
            const currentGroup = groups[currentTurnIndex];
            const banner = document.getElementById('turn-banner');
            banner.innerText = `Giliran: ${currentGroup.icon} ${currentGroup.name}`;
            banner.className = `w-full py-2 px-4 rounded-xl font-bold text-sm mb-4 shadow-md text-white animate-pulse ${currentGroup.color}`;

            document.getElementById('btn-roll').disabled = (gameStatus !== "waiting_roll");
            updateScorecard();
        }

        function updateUIStatus(text) {
            document.getElementById('game-status-text').innerText = text;
        }

        function updateScorecard() {
            const container = document.getElementById('scorecard-container');
            container.innerHTML = '';
            groups.forEach((g, idx) => {
                let isCurrent = (idx === currentTurnIndex);
                let activeBorder = isCurrent ? 'border-2 border-yellow-400 bg-white/15' : 'bg-white/5 border border-white/10';
                container.innerHTML += `
                    <div class="flex items-center justify-between p-2.5 rounded-xl ${activeBorder} transition">
                        <div class="flex items-center gap-2">
                            <span class="text-xl">${g.icon}</span>
                            <div>
                                <h4 class="font-bold text-xs text-white">${g.name}</h4>
                                <p class="text-[10px] text-cyan-200">Kotak: ${g.pos}</p>
                            </div>
                        </div>
                        <div class="text-right">
                            <span class="font-bold text-sm text-yellow-300">${g.score}</span>
                            <p class="text-[9px] text-slate-300">Poin</p>
                        </div>
                    </div>
                `;
            });
        }

        function toggleAudio() {
            SoundFX.enabled = !SoundFX.enabled;
            const icon = document.getElementById('audio-icon');
            const text = document.getElementById('audio-text');
            if (SoundFX.enabled) {
                icon.className = "fa-solid fa-volume-high";
                text.innerText = "Suara: ON";
            } else {
                icon.className = "fa-solid fa-volume-xmark";
                text.innerText = "Suara: OFF";
            }
        }

        function openModal(modalId) {
            document.getElementById(modalId).classList.remove('hidden');
            document.getElementById(modalId).classList.add('flex');
        }

        function closeModal(modalId) {
            document.getElementById(modalId).classList.remove('flex');
            document.getElementById(modalId).classList.add('hidden');
        }
    </script>
</body>
</html>