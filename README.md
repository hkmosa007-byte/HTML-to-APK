<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8" />
  <!-- Updated Viewport with viewport-fit=cover for safe area handling -->
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover, interactive-widget=resizes-content" />
  <title>NU Smart Question Bank</title>

  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>

  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+Bengali:wght@300;400;500;600;700&family=Noto+Serif+Bengali:wght@400;500;600;700&display=swap" rel="stylesheet">

  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: {
            sans: ['"Noto Sans Bengali"', 'sans-serif'],
            serif: ['"Noto Serif Bengali"', 'serif'],
          },
          colors: {
            primary: {
              50: '#fff1f2', 100: '#ffe4e6', 200: '#fecdd3', 300: '#fda4af',
              400: '#fb7185', 500: '#f43f5e', 600: '#e11d48', 700: '#be123c',
              800: '#9f1239', 900: '#881337',
            },
            accent: {
              500: '#10b981', 600: '#059669',
            }
          },
          animation: {
            'fade-in': 'fadeIn 0.5s ease-out',
            'slide-up': 'slideUp 0.4s ease-out',
            'bounce-slow': 'bounce 2s infinite',
          },
          keyframes: {
            fadeIn: { '0%': { opacity: '0' }, '100%': { opacity: '1' } },
            slideUp: { '0%': { transform: 'translateY(20px)', opacity: '0' }, '100%': { transform: 'translateY(0)', opacity: '1' } }
          }
        }
      }
    }
  </script>

  <style>
    body { font-family: 'Noto Sans Bengali', sans-serif; background-color: #fff1f2; color: #1f2937; overflow-x: hidden; }
    .font-content { font-family: 'Noto Serif Bengali', serif; }
    
    /* Header safe area handling */
    .header-safe-area {
      padding-top: env(safe-area-inset-top);
      height: auto;
    }
    
    .bg-glass { background: rgba(255, 255, 255, 0.90); backdrop-filter: blur(12px); -webkit-backdrop-filter: blur(12px); border-bottom: 1px solid rgba(255, 228, 230, 0.5); }
    .mesh-bg {
      position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; z-index: -1;
      background: radial-gradient(at 0% 0%, rgba(225, 29, 72, 0.12) 0px, transparent 50%),
                  radial-gradient(at 100% 0%, rgba(244, 63, 94, 0.1) 0px, transparent 50%),
                  radial-gradient(at 100% 100%, rgba(251, 113, 133, 0.08) 0px, transparent 50%),
                  radial-gradient(at 0% 100%, rgba(225, 29, 72, 0.05) 0px, transparent 50%);
      background-size: cover;
    }
    ::-webkit-scrollbar { width: 8px; }
    ::-webkit-scrollbar-track { background: transparent; }
    ::-webkit-scrollbar-thumb { background: #fda4af; border-radius: 4px; }
    ::-webkit-scrollbar-thumb:hover { background: #fb7185; }
    .modern-input { transition: all 0.2s ease; border: 1px solid #fecdd3; background: #ffffff; }
    .modern-input:focus { border-color: #e11d48; box-shadow: 0 0 0 4px rgba(225, 29, 72, 0.1); outline: none; }
    .form-label-float { pointer-events: none; transition: all 0.2s ease; }
    .modern-input:focus ~ .form-label-float, .modern-input:not(:placeholder-shown) ~ .form-label-float {
      transform: translateY(-26px) scale(0.85); color: #e11d48; background-color: white; padding: 0 4px; margin-left: -4px;
    }
    .spinner { width: 20px; height: 20px; border-radius: 50%; border: 2px solid rgba(255,255,255,0.3); border-top-color: #fff; animation: spin 0.8s linear infinite; }
    @keyframes spin { to { transform: rotate(360deg); } }
    .dots-loader div { width: 10px; height: 10px; border-radius: 50%; background-color: #e11d48; animation: bounce 1.4s infinite ease-in-out both; }
    .dots-loader .dot1 { animation-delay: -0.32s; } .dots-loader .dot2 { animation-delay: -0.16s; }
    @keyframes bounce { 0%, 80%, 100% { transform: scale(0); } 40% { transform: scale(1); } }
    .tab-btn { position: relative; transition: color 0.3s; white-space: nowrap; }
    .tab-active::after { content: ''; position: absolute; bottom: -2px; left: 0; width: 100%; height: 3px; background: #e11d48; border-radius: 3px 3px 0 0; transform: scaleX(1); transition: transform 0.3s ease; }
    .tab-inactive::after { content: ''; position: absolute; bottom: -2px; left: 0; width: 100%; height: 3px; background: transparent; transform: scaleX(0); transition: transform 0.3s ease; }
    .answer-content { overflow: hidden; max-height: 0; transition: max-height 0.3s cubic-bezier(0, 1, 0, 1); }
    .answer-content.show { max-height: 2000px; transition: max-height 0.5s ease-in-out; }
    #toast-container { position: fixed; bottom: 24px; right: 24px; z-index: 100; display: flex; flex-direction: column; gap: 10px; }
    
    /* Modified Chat Animation Classes */
    .chat-open { opacity: 1; transform: translateY(0) scale(1); pointer-events: all; }
    .chat-closed { opacity: 0; transform: translateY(20px) scale(0.95); pointer-events: none; }
    
    /* PDF Generation specific styles */
    .card { break-inside: avoid; page-break-inside: avoid; margin-bottom: 15px; }
    
    /* Chat bubbles */
    .chat-bubble-ai strong { font-weight: 700; color: #1f2937; }
    .chat-bubble-ai ul { list-style-type: disc; padding-left: 1.2em; margin-top: 0.5em; margin-bottom: 0.5em; }
    .chat-bubble-ai li { margin-bottom: 0.25em; }
    .chat-bubble-ai p { margin-bottom: 0.5em; }
    .typing-dot { animation: typing 1.4s infinite ease-in-out both; width: 5px; height: 5px; border-radius: 50%; background-color: #9CA3AF; margin: 0 2px; }
    .typing-dot:nth-child(1) { animation-delay: -0.32s; } .typing-dot:nth-child(2) { animation-delay: -0.16s; }
    @keyframes typing { 0%, 80%, 100% { transform: scale(0); } 40% { transform: scale(1); } }

    /* Checkbox Styles */
    .checkbox-wrapper input:checked + div { background-color: #e11d48; border-color: #e11d48; }
    .checkbox-wrapper input:checked + div svg { display: block; }
  </style>
</head>

<body class="antialiased min-h-screen flex flex-col">
  <div class="mesh-bg"></div>

  <!-- Header Updated: sticky top-0 with padding-top for safe area -->
  <header class="sticky top-0 z-50 bg-glass shadow-sm transition-all duration-300 header-safe-area">
    <div class="max-w-5xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex justify-between items-center h-16 md:h-20">
        <div class="flex items-center gap-3 cursor-pointer group" onclick="location.reload()">
          <!-- Updated Logo: Book Icon matching color theme -->
          <div class="w-10 h-10 md:w-11 md:h-11 rounded-xl bg-gradient-to-br from-primary-600 to-primary-700 text-white flex items-center justify-center shadow-lg group-hover:scale-105 transition-transform shadow-primary-500/30">
            <svg class="w-6 h-6 md:w-7 md:h-7" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253"></path></svg>
          </div>
          <div>
            <h1 class="text-xl md:text-2xl font-bold text-gray-900 tracking-tight leading-none group-hover:text-primary-700 transition-colors">Smart Question Bank</h1>
            <p class="text-xs md:text-sm text-gray-500 font-medium">AI Powered • Social Work</p>
          </div>
        </div>
        <div class="flex items-center gap-3 md:gap-4">
          <button id="header-saved-btn" class="relative p-2 rounded-full hover:bg-primary-50 transition text-gray-600 hover:text-primary-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-primary-500" title="সংরক্ষিত প্রশ্ন">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M5 5a2 2 0 012-2h10a2 2 0 012 2v16l-7-3.5L5 21V5z"></path></svg>
            <span id="saved-count-badge" class="absolute top-1 right-0.5 min-w-[18px] h-[18px] bg-accent-600 text-white text-[10px] font-bold flex items-center justify-center rounded-full border-2 border-white shadow-sm">0</span>
          </button>
        </div>
      </div>
    </div>
  </header>

  <main class="flex-grow w-full max-w-5xl mx-auto px-4 sm:px-6 py-6 md:py-8 space-y-6">
    <div class="bg-primary-50 border border-primary-100 rounded-2xl p-4 flex items-start gap-3 shadow-sm animate-fade-in">
      <svg class="w-5 h-5 text-primary-600 mt-0.5 flex-shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg>
      <p class="text-sm text-primary-800 leading-relaxed">এই অ্যাপটি বর্তমানে শুধুমাত্র জাতীয় বিশ্ববিদ্যালয়ের অনার্স <span class="font-bold">সমাজকর্ম (Social Work)</span> বিভাগের জন্য ডিজাইন করা হয়েছে।</p>
    </div>

    <nav class="bg-white rounded-2xl p-1.5 shadow-sm border border-primary-100 flex gap-1 relative overflow-x-auto no-scrollbar" role="tablist">
      <button id="tab-btn-generate" class="tab-btn flex-1 py-3 px-4 text-sm md:text-base font-semibold rounded-xl text-center text-gray-500 hover:bg-primary-50 hover:text-primary-600 focus:outline-none focus:text-primary-700 transition-all tab-active">প্রশ্ন জেনারেট</button>
      <button id="tab-btn-tracker" class="tab-btn flex-1 py-3 px-4 text-sm md:text-base font-semibold rounded-xl text-center text-gray-500 hover:bg-primary-50 hover:text-primary-600 focus:outline-none focus:text-primary-700 transition-all tab-inactive">সিলেবাস ট্র্যাকার</button>
      <button id="tab-btn-collection" class="tab-btn flex-1 py-3 px-4 text-sm md:text-base font-semibold rounded-xl text-center text-gray-500 hover:bg-primary-50 hover:text-primary-600 focus:outline-none focus:text-primary-700 transition-all tab-inactive">সংগ্রহশালা</button>
    </nav>

    <div id="tab-content-container" class="relative min-h-[400px]">
      
      <section id="tab-content-generate" class="animate-fade-in space-y-6" aria-labelledby="tab-btn-generate">
        <div class="bg-white rounded-3xl p-6 md:p-8 shadow-lg shadow-primary-500/5 border border-white">
          <div class="flex items-center gap-3 mb-6">
            <div class="p-2 bg-primary-50 rounded-lg text-primary-600">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19.428 15.428a2 2 0 00-1.022-.547l-2.384-.477a6 6 0 00-3.86.517l-.318.158a6 6 0 01-3.86.517L6.05 15.21a2 2 0 00-1.806.547M8 4h8l-1 1v5.172a2 2 0 00.586 1.414l5 5c1.26 1.26.367 3.414-1.415 3.414H4.828c-1.782 0-2.674-2.154-1.414-3.414l5-5A2 2 0 009 10.172V5L8 4z"></path></svg>
            </div>
            <h2 class="text-xl font-bold text-gray-800">কাস্টম প্রশ্ন তৈরি করুন</h2>
          </div>
          <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
            <div class="relative group col-span-1 md:col-span-2">
              <select id="year-select" class="modern-input w-full px-4 py-3.5 rounded-xl appearance-none text-gray-700 font-medium cursor-pointer placeholder-transparent bg-transparent z-10 relative"><option value=""> </option></select>
              <label class="form-label-float absolute left-4 top-3.5 text-gray-400 text-sm z-0">বর্ষ নির্বাচন করুন (Year)</label>
              <div class="absolute right-4 top-1/2 transform -translate-y-1/2 pointer-events-none text-gray-400"><svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path></svg></div>
            </div>
            <div class="relative group md:col-span-1">
              <select id="subject-select" disabled class="modern-input w-full px-4 py-3.5 rounded-xl appearance-none text-gray-700 font-medium cursor-pointer disabled:bg-gray-50 disabled:cursor-not-allowed"><option value=""> </option></select>
              <label class="form-label-float absolute left-4 top-3.5 text-gray-400 text-sm">বিষয় (Subject)</label>
              <div class="absolute right-4 top-1/2 transform -translate-y-1/2 pointer-events-none text-gray-400"><svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path></svg></div>
            </div>
            <div class="relative group md:col-span-1">
              <select id="chapter-select" disabled class="modern-input w-full px-4 py-3.5 rounded-xl appearance-none text-gray-700 font-medium cursor-pointer disabled:bg-gray-50 disabled:cursor-not-allowed"><option value=""> </option></select>
              <label class="form-label-float absolute left-4 top-3.5 text-gray-400 text-sm">অধ্যায় / টপিক</label>
              <div class="absolute right-4 top-1/2 transform -translate-y-1/2 pointer-events-none text-gray-400"><svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path></svg></div>
            </div>
            <div class="relative group">
              <select id="type" class="modern-input w-full px-4 py-3.5 rounded-xl appearance-none text-gray-700 font-medium cursor-pointer">
                <option value="Mixed">Mixed (সব ধরনের)</option><option value="MCQ">MCQ</option><option value="Short">Short (সংক্ষিপ্ত)</option><option value="Long">Long (রচনামূলক)</option>
              </select>
              <label class="form-label-float absolute left-4 top-3.5 text-gray-400 text-sm">প্রশ্নের ধরন</label>
              <div class="absolute right-4 top-1/2 transform -translate-y-1/2 pointer-events-none text-gray-400"><svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path></svg></div>
            </div>
            <div class="relative group">
              <input type="number" id="question-count" value="10" min="5" max="50" placeholder=" " class="modern-input w-full px-4 py-3.5 rounded-xl text-gray-700 font-medium">
              <label class="form-label-float absolute left-4 top-3.5 text-gray-400 text-sm">প্রশ্নের সংখ্যা (৫-৫০)</label>
            </div>
          </div>
          <button id="generate-btn" disabled class="mt-8 w-full bg-gradient-to-r from-primary-600 to-primary-700 hover:from-primary-700 hover:to-primary-800 text-white font-bold py-4 px-6 rounded-xl shadow-lg shadow-primary-500/30 transform hover:-translate-y-0.5 transition-all duration-200 disabled:opacity-50 disabled:cursor-not-allowed disabled:shadow-none flex items-center justify-center gap-3">
            <span id="btn-text">প্রশ্ন জেনারেট করুন</span>
            <div id="loader" class="hidden"><div class="spinner"></div></div>
            <svg id="btn-icon" class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"></path></svg>
          </button>
        </div>
        <div id="result-container" class="space-y-5 pb-10">
          <div id="message-area" class="flex flex-col items-center justify-center py-16 text-center text-gray-400">
            <div class="w-24 h-24 bg-white rounded-full flex items-center justify-center mb-4 shadow-sm border border-primary-50">
              <svg class="w-10 h-10 text-primary-200" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M19.428 15.428a2 2 0 00-1.022-.547l-2.384-.477a6 6 0 00-3.86.517l-.318.158a6 6 0 01-3.86.517L6.05 15.21a2 2 0 00-1.806.547M8 4h8l-1 1v5.172a2 2 0 00.586 1.414l5 5c1.26 1.26.367 3.414-1.415 3.414H4.828c-1.782 0-2.674-2.154-1.414-3.414l5-5A2 2 0 009 10.172V5L8 4z"></path></svg>
            </div>
            <p class="text-lg font-medium">শুরু করতে উপরের ফর্মটি পূরণ করুন</p>
          </div>
        </div>
      </section>

      <section id="tab-content-tracker" class="hidden animate-fade-in space-y-6" aria-labelledby="tab-btn-tracker">
        <div class="bg-gradient-to-r from-primary-600 to-primary-700 rounded-3xl p-6 md:p-8 shadow-lg text-white">
          <div class="flex flex-col md:flex-row justify-between items-center gap-6">
            <div>
              <h2 class="text-2xl font-bold mb-2">সিলেবাস অগ্রগতি ড্যাশবোর্ড</h2>
              <p class="text-primary-100 text-sm">চ্যাপ্টার শেষ করে চেকলিস্ট আপডেট করুন</p>
            </div>
            <div class="flex-1 w-full md:max-w-md bg-white/10 p-4 rounded-xl border border-white/20 backdrop-blur-sm">
              <div class="flex justify-between items-end mb-2">
                <span class="text-sm font-medium text-primary-50" id="tracker-summary-text">লোডিং...</span>
                <span class="text-2xl font-bold" id="tracker-percent">0%</span>
              </div>
              <div class="w-full bg-black/20 rounded-full h-3">
                <div id="tracker-progress-bar" class="bg-accent-500 h-3 rounded-full transition-all duration-700 ease-out" style="width: 0%"></div>
              </div>
            </div>
          </div>
        </div>

        <div class="flex justify-end">
          <div class="relative group w-full md:w-64">
            <select id="tracker-year-filter" class="modern-input w-full px-4 py-2.5 rounded-xl appearance-none text-gray-700 font-medium cursor-pointer">
              </select>
            <div class="absolute right-4 top-1/2 transform -translate-y-1/2 pointer-events-none text-gray-400"><svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path></svg></div>
          </div>
        </div>

        <div id="tracker-list-container" class="space-y-4 pb-10">
          </div>
      </section>

      <section id="tab-content-collection" class="hidden animate-fade-in" aria-labelledby="tab-btn-collection">
        <div class="bg-white rounded-3xl p-6 md:p-8 shadow-lg shadow-primary-500/5 border border-white min-h-[500px]">
          <div class="flex flex-wrap items-center justify-between mb-8 gap-4">
            <h2 class="text-xl font-bold text-gray-800 flex items-center gap-2">
              <span class="p-1.5 bg-yellow-100 rounded-lg text-yellow-600"><svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 5a2 2 0 012-2h10a2 2 0 012 2v16l-7-3.5L5 21V5z"></path></svg></span>
              সংরক্ষিত প্রশ্ন
            </h2>
            <div class="flex items-center gap-3">
              <button id="download-pdf-btn" class="text-xs font-semibold bg-primary-50 text-primary-700 px-4 py-2.5 rounded-xl hover:bg-primary-100 transition shadow-sm border border-primary-100 flex items-center gap-2">
                 <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4"></path></svg>
                 PDF Download
              </button>
              
              <button id="export-csv-btn" class="text-xs font-semibold bg-gray-50 text-gray-700 px-4 py-2.5 rounded-xl hover:bg-gray-100 transition shadow-sm border border-gray-100">Export CSV</button>
              <button id="clear-all-btn" class="text-xs font-semibold bg-red-50 text-red-700 px-4 py-2.5 rounded-xl hover:bg-red-100 transition shadow-sm border border-red-100">Clear All</button>
            </div>
          </div>
          <div id="collection-container" class="space-y-5"></div>
        </div>
      </section>
    </div>
  </main>

  <footer class="mt-auto py-8 text-center text-gray-400 text-sm font-medium border-t border-primary-100 bg-white/50 backdrop-blur-sm pb-32 md:pb-8">
    <p>&copy; Shahadot 2025. All rights reserved.</p>
  </footer>

  <!-- FAB updated position: Right Side and slightly higher -->
  <button id="chat-fab" class="fixed bottom-6 right-6 z-40 w-14 h-14 bg-gradient-to-r from-primary-600 to-primary-700 text-white rounded-full shadow-xl hover:shadow-2xl hover:scale-110 transition-all duration-300 flex items-center justify-center group border-2 border-white">
    <svg class="w-7 h-7 group-hover:animate-bounce-slow" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z"></path></svg>
    <span class="absolute -top-1 -right-1 w-4 h-4 bg-green-500 rounded-full border-2 border-white"></span>
  </button>

  <!-- Updated Chat Widget Structure for Right Side -->
  <div id="chat-widget" class="chat-closed fixed z-50 flex flex-col transition-all duration-300 origin-bottom-right bg-white shadow-2xl 
       md:bottom-24 md:right-6 md:w-96 md:h-[500px] md:rounded-2xl md:border md:border-primary-100
       bottom-0 right-0 w-full h-[100dvh] rounded-none border-none">
       
    <div class="p-4 bg-gradient-to-r from-primary-600 to-primary-700 text-white md:rounded-t-2xl flex justify-between items-center shadow-sm flex-none">
      <div class="flex items-center gap-3">
         <div class="w-2.5 h-2.5 bg-green-400 rounded-full animate-pulse shadow-[0_0_8px_rgba(74,222,128,0.6)]"></div>
         <div><h3 class="font-bold text-sm md:text-base">AI স্টাডি অ্যাসিস্ট্যান্ট</h3><p class="text-[10px] md:text-xs opacity-90 text-primary-100">সব সময় আপনার সাথে</p></div>
      </div>
      <button id="close-chat-btn" class="hover:bg-white/20 p-1.5 rounded-lg transition-colors"><svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path></svg></button>
    </div>
    
    <div id="chat-body" class="flex-1 overflow-y-auto p-4 space-y-4 bg-slate-50 scroll-smooth">
      <div class="flex gap-3 items-start">
         <div class="w-8 h-8 rounded-full bg-primary-100 border border-primary-200 flex items-center justify-center text-primary-600 text-xs font-bold flex-shrink-0">AI</div>
         <div class="bg-white p-3.5 rounded-2xl rounded-tl-none shadow-sm text-sm text-gray-700 border border-gray-100 leading-relaxed">হ্যালো! 👋 আমি আপনার পার্সোনাল স্টাডি অ্যাসিস্ট্যান্ট। সমাজকর্ম বিষয়ক যেকোনো প্রশ্ন আমাকে করতে পারেন।</div>
      </div>
    </div>
    
    <div class="p-3 border-t border-gray-100 bg-white md:rounded-b-2xl flex-none pb-safe">
      <div class="relative flex items-center gap-2">
        <input type="text" id="chat-input" placeholder="আপনার প্রশ্ন লিখুন..." class="flex-1 bg-gray-50 border border-gray-200 rounded-xl pl-4 pr-3 py-3 text-sm focus:outline-none focus:border-primary-400 focus:ring-2 focus:ring-primary-100 transition-all">
        <button id="send-msg-btn" class="bg-primary-600 hover:bg-primary-700 text-white p-3 rounded-xl transition-all shadow-md hover:shadow-lg transform active:scale-95 disabled:opacity-50 disabled:cursor-not-allowed"><svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8"></path></svg></button>
      </div>
    </div>
  </div>

  <div id="toast-container"></div>

  <script>
    // --- API Configuration ---
    const apiKey = ""; 
    const geminiModel = 'gemini-2.5-flash-preview-09-2025';
    const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/${geminiModel}:generateContent?key=${apiKey}`;

    // --- State Keys ---
    const LS_KEY_QUESTION_GEN= 'nuSmartApp_questionGenResult_v1';
    const LS_KEY_YEAR = 'nuSmartApp_form_year_v1';
    const LS_KEY_SUBJECT = 'nuSmartApp_form_subject_v1';
    const LS_KEY_CHAPTER = 'nuSmartApp_form_chapter_v1';
    const LS_KEY_SAVED = 'nuSmartApp_savedQuestions_v1';
    const LS_KEY_SYLLABUS = 'nuSmartApp_syllabus_v1';

    // --- Data ---
    const subjectData = {
      "1st Year":[{"name":"সমাজকর্ম পরিচিতি (Introduction to Social Work)","chapters":["সমাজকর্মের সংজ্ঞা ও ধারণা","সমাজকর্ম ও সমাজকল্যাণের পার্থক্য","সমাজসেবা (Social Service) ও সমাজবিকাশ (Social Development)","সামাজিক সমস্যা (Social Problems)","সামাজিক পরিবর্তন (Social Change)","সামাজিক নিয়ন্ত্রন (Social Control)","সামাজিক সংস্কার (Social Reform)","সমাজকর্মের ‘ উদ্ভব ও বিকাশ ’ (Historical Development)","সমাজকর্মের উদ্দেশ্য ও কার্যাবলী","সমাজকর্মের নৈতিক / নৈতিকবিধি (Ethics of Social Work)","পেশাগত সমাজকর্ম (Professional Social Work)","সমাজকর্ম ও মানবিক মূল্যবোধ","সমাজকর্ম ও অন্যান্য শাস্ত্রের সম্পর্ক ( সামাজিক বিজ্ঞান , মনোবিজ্ঞান ইত্যাদি )","বাংলাদেশে সমাজকর্মের অগ্রগতি ও সমসাময়িক চ্যালেঞ্জ","আন্তর্জাতিক সমাজকর্ম প্রবণতা ও উদাহরণ"]},{"name":"বাংলাদেশ অধ্যয়ন : ইতিহাস , সংস্কৃতি ও ঐতিহ্য","chapters":["ভূগোল ও প্রাকগোল","প্রাচীন বাংলা / প্রাগৈতিহাসিক এবং প্রাচীন সভ্যতা","মধ্যযুগীয় বাংলা","বাংলা মুসলিম শাসন and মুসলিম বাংলা","বাঙালি সংস্কৃতি ও ভাষার বিকাশ","বাংলা সাহিত্যের ইতিহাস ও গঠন","ধর্ম ও আধ্যাত্মিকতা : ইসলাম , হিন্দু , বৌদ্ধ , জৈন ইত্যাদি","আরব , ফারসি ও উপনিবেশিক প্রভাব","ব্রিটিশ ভারত ও উপনিবেশবাদ","আন্দোলন ও বাঙালী রূপান্তর ( ১৮৫৭ মিউটিনি , বঙ্গভঙ্গ , মুসলিম লিগ – জাতীয়তাবাদ )","ভাষা আন্দোলন ও সাংস্কৃতিক চেতনা","১৯৪৭ পরবর্তী অবস্থা : পূর্ব গোড়াপত্তন","মুক্তিযুদ্ধ ১৯৭১ : কারণ , ঘটনা , পরিণতি","স্বাধীন বাংলাদেশের ইতিহাস ও রাষ্ট্র গঠন","জাতীয় প্রতীক , স্মৃতিসৌধ ও সংগ্রহশালা","লোকসভ্যতা ও قومی ঐতিহ্য","নৃতত্ত্ব ও আদিবাসী সংস্কৃতি","সাংস্কৃতিক শিল্পকলাসমূহ ( সংগীত , নাটক , নৃত্য , চিত্রকলা , হস্তশিল্প )","আধুনিক বাংলাদেশ : পরিবর্তন ও চ্যালেঞ্জ","আন্তর্জাতিক প্রভাব ও বৈচিত্র্য"]},{"name":"সমাজকর্মের ইতিহাস ও দর্শন","chapters":["সমাজকর্মের ইতিহাস : প্রারম্ভিক যুগ","সমাজকর্মের আধুনিকীকরণ এবং বিকাশ","সমাজকর্মের মূল ধারণা ও দর্শন","সমাজকর্মের নৈতিক ও আদর্শগত ভিত্তি","সমাজকর্মের বিভিন্ন দার্শনিক প্রবণতা","সমাজকর্মের ধর্মীয় ও মানবতাবাদী দৃষ্টিভঙ্গি","সমাজকর্মের বৈজ্ঞানিক ও প্রাতিষ্ঠানিক বিকাশ","বিশ্বব্যাপী সমাজকর্মের ইতিহাস ও প্রভাব","বাংলাদেশে সমাজকর্মের ইতিহাস ও চলমান চ্যালেঞ্জ","সমাজকর্মের ভবিষ্যৎ ধারা ও চ্যালেঞ্জ"]},{"name":"মানব মনোবিজ্ঞান ও সমাজকর্ম","chapters":["মনোবিজ্ঞানের সংজ্ঞা , বিষয় ও প্রকারভেদ","মানব আচরণ ও বিকাশ","মনোবিজ্ঞানের প্রধান প্রবাহসমূহ (Behaviorism, Psychoanalysis, Humanistic Psychology ইত্যাদি )","আবেগ , বুদ্ধিমত্তা ও ব্যক্তিত্বের পরিচিতি","স্মৃতি ও শেখার প্রক্রিয়া","মনোবৈজ্ঞানিক পরীক্ষণ ও মূল্যায়ন","মানসিক রোগ ও বিকৃতি","সমাজকর্মে মনোবিজ্ঞানের প্রয়োগ","মানসিক স্বাস্থ্য ও সমাজকর্ম"," শিশু , কিশোর ও প্রবীণদের মনোবিজ্ঞান","মনোবিজ্ঞানের সাহায্যে ক্লায়েন্টের সহায়তা ও সমস্যা সমাধান","মনোবৈজ্ঞানিক থেরাপি ও সামাজিক পুনর্বাসন"]},{"name":"অর্থনীতি ও উন্নয়ন","chapters":["অর্থনীতির মৌলিক ধারণা ও শাখাসমূহ","সম্পদের শ্রেণীবিভাগ ও অর্থনৈতিক সমস্যা","চাহিদা ও যোগান","উৎপাদন , উপযোগিতা ও খরচ","বাজার ও মূল্য নির্ধারণ","মাক্রোইকোনমিক্স : মোট চাহিদা ও মোট যোগান","অর্থনৈতিক বৃদ্ধি ও উন্নয়নের ধারণা","উন্নয়ন অর্থনীতির বৈশিষ্ট্য ও লক্ষণ","দারিদ্র্য , বেকারত্ব ও অসমতা","উন্নয়ন নীতিমালা ও পরিকল্পনা","বাংলাদেশে অর্থনৈতিক উন্নয়ন : সমস্যা ও সম্ভাবনা","বৈশ্বিক অর্থনীতি ও উন্নয়ন সহযোগিতা","পরিবেশ ও টেকসই উন্নয়ন","অর্থনৈতিক সংস্কার ও আধুনিকায়ন","অর্থনীতি ও সমাজকর্মের সংযোগ"]},{"name":"বাংলাদেশ স্বাধীনতা যুদ্ধের ইতিহাস","chapters":["পূর্ব বাংলার রাজনৈতিক ও সামাজিক প্রেক্ষাপট ( ১৯৪৭ - ১৯৭০ )","১৯৪৭ থেকে ১৯৭১ পর্যন্ত পাকিস্তান শাসন","ভাষা আন্দোলন ( ১৯৫২ ) ও এর প্রভাব","৬ দফা আন্দোলন ( ১৯৬৬ )","১৯৭০ সালের সাধারণ নির্বাচন ও এর ফলাফল","বাংলাদেশের মুক্তিযুদ্ধের সূচনা ( ২৬ মার্চ ১৯৭১ )","মুক্তিযুদ্ধের প্রধান ঘটনা ও যুদ্ধকৌশল","স্বাধীনতা যুদ্ধের নেতা ও সংগঠনসমূহ","মুক্তিযুদ্ধের আন্তর্জাতিক প্রেক্ষাপট ও সাহায্য","১৬ ডিসেম্বর ১৯৭১ : বিজয় দিবস ও মুক্তিযুদ্ধের সমাপ্তি","মুক্তিযুদ্ধের পরবর্তী বাংলাদেশ : রাজনীতি ও সমাজ পরিবর্তন","মুক্তিযুদ্ধের স্মৃতি ও জাতীয় ঐতিহ্য সংরক্ষণ"]}],
      "2nd Year":[{"name":"মানব জীববিদ্যা : বৃদ্ধি ও বিকাশ","chapters":["মানব জীববিদ্যার পরিচিতি ও গুরুত্ব","মানব দেহের গঠন : কোষ , টিস্যু ও অঙ্গপ্রত্যঙ্গ","মানব দেহের প্রধান সিস্টেমসমূহ ( পেশী , কঙ্কাল , স্নায়ু , রক্ত সঞ্চালন ইত্যাদি )","মানব বৃদ্ধি ও বিকাশের ধাপসমূহ ( শিশু , কৈশোর , প্রাপ্তবয়স্ক , বৃদ্ধ )","মানব জীবনে পুষ্টি ও খাদ্যের ভূমিকা","মানব শারীরবৃত্তীয় পরিবর্তনসমূহ ( হরমোন , প্রজনন প্রক্রিয়া )","রোগ ও রোগ প্রতিরোধ ক্ষমতা","পরিবেশ ও স্বাস্থ্য : প্রভাব ও সংরক্ষণ","মানব বিকাশে সামাজিক ও মানসিক প্রভাব","স্বাস্থ্য সচেতনতা ও স্বাস্থ্যকর জীবনযাপন","শিশু ও কিশোরের বিকাশের বৈজ্ঞানিক দিক","বৃদ্ধাবস্থার শারীরিক ও মানসিক পরিবর্তন"]},{"name":"সামাজিক নীতি ও পরিকল্পনা","chapters":["সামাজিক নীতির ধারণা , প্রয়োজনীয়তা ও উদ্দেশ্য","সামাজিক নীতির প্রকারভেদ ও বৈশিষ্ট্য","সামাজিক নীতির পরিকল্পনা ও প্রণয়ন প্রক্রিয়া","সামাজিক নীতির মূল্যায়ন ও বাস্তবায়ন","সামাজিক নীতির ইতিহাস ও উন্নয়ন","বিভিন্ন দেশে সামাজিক নীতির উদাহরণ","বাংলাদেশের সামাজিক নীতি ও পরিকল্পনা","দারিদ্র্য বিমোচন নীতি ও কর্মসূচি","শিক্ষা , স্বাস্থ্য ও জনসংখ্যা নীতি","কর্মসংস্থান ও শ্রম নীতি","নারী ও শিশুবিষয়ক সামাজিক নীতি","দুর্যোগ ব্যবস্থাপনা ও সামাজিক সুরক্ষা নীতি","আন্তর্জাতিক সামাজিক নীতি ও মানবাধিকার","সামাজিক নীতির চ্যালেঞ্জ ও ভবিষ্যত সম্ভাবনা"]},{"name":"সামাজিক সমস্যা বিশ্লেষণ","chapters":["সামাজিক সমস্যার সংজ্ঞা ও বৈশিষ্ট্য","সামাজিক সমস্যার কারণ ও প্রকারভেদ","দারিদ্র্য ও দারিদ্র্য বিমোচন","বেকারত্ব ও কর্মসংস্থান সমস্যা","অপরাধ ও নৃশংসতা","গৃহহীনতা ও বাসস্থান সমস্যা","মাদকাসক্তি ও নেশা সমস্যা","লিঙ্গ বৈষম্য ও নারী নির্যাতন","শিশু ও কিশোর সমস্যা","পারিবারিক সমস্যা ও বিবাহ বিচ্ছেদ","সামাজিক সমস্যা সমাধানে সরকারী ও বেসরকারী উদ্যোগ","সামাজিক আন্দোলন ও পরিবর্তন","সাম্প্রদায়িকতা ও জাতীয়তা সমস্যা","আধুনিকায়ন ও সামাজিক সমস্যা","সামাজিক সমস্যার তত্ত্ব ও বিশ্লেষণ পদ্ধতি"]},{"name":"নৃতত্ত্ব পরিচিতি (Introduction to Anthropology)","chapters":["নৃতত্ত্বের সংজ্ঞা , উদ্দেশ্য ও গুরুত্ব","নৃতত্ত্বের শাখা ও বিষয়বস্তু","মানুষের বিবর্তন ও নৃতত্ত্বের ইতিহাস","সাংস্কৃতিক নৃতত্ত্ব : সংস্কৃতি ও সমাজ","ভাষা ও যোগাযোগের ভূমিকা","নৃতত্ত্বের গবেষণা পদ্ধতি ও সরঞ্জাম","সামাজিক কাঠামো ও সামাজিক প্রতিষ্ঠান","ধর্ম ও আধ্যাত্মিকতা","পরিবার ও বংশপরম্পরা","নৃতাত্ত্বিক বিভাজন ও জাতি","অর্থনীতি ও বিনিময় ব্যবস্থা","রাজনৈতিক ব্যবস্থা ও ক্ষমতা","নৃতাত্ত্বিক পরিবর্তন ও আধুনিকতা","বাংলাদেশে নৃতাত্ত্বিক বৈচিত্র্য ও আদিবাসী জনগোষ্ঠী"]},{"name":"কম্পিউটার ও তথ্য প্রযুক্তি","chapters":["কম্পিউটারের পরিচিতি ও ইতিহাস","কম্পিউটারের ধরন ও উপাদান","হার্ডওয়্যার এবং সফটওয়্যার","অপারেটিং সিস্টেমের ধারণা","তথ্য ও তথ্য প্রযুক্তির ভূমিকা","মাইক্রোসফট অফিস : ওয়ার্ড , এক্সেল , পাওয়ারপয়েন্টের পরিচিতি","ইন্টারনেট ও ইমেইল ব্যবহার","ডেটাবেস ও তথ্য সংরক্ষণ","কম্পিউটার নেটওয়ার্ক ও যোগাযোগ ব্যবস্থা","সাইবার নিরাপত্তা ও তথ্য সুরক্ষা","তথ্য প্রযুক্তি ও সমাজ","আধুনিক প্রযুক্তির ব্যবহার ও প্রভাব","মোবাইল কম্পিউটিং ও স্মার্ট ডিভাইস","ক্লাউড কম্পিউটিং ও ডিজিটাল ডেটা","তথ্য প্রযুক্তিতে নতুন প্রবণতা ও ভবিষ্যৎ দিকনির্দেশনা"]},{"name":"সমাজতত্ত্ব পরিচিতি (Introduction to Sociology)","chapters":["সমাজতত্ত্বের সংজ্ঞা ও অর্থ","সমাজ ও সামাজিক প্রতিষ্ঠান","সংস্কৃতি ও সামাজিকীকরণ","সামাজিক শ্রেণি ও স্তরবিন্যাস","সামাজিক পরিবর্তন ও প্রক্রিয়া","সামাজিক নিয়ন্ত্রণ ও আইন","পারিবারিক প্রতিষ্ঠান","ধর্ম ও সমাজ","জনসংখ্যা ও নগরায়ন","সামাজিক সমস্যা ও সমাধান","সমাজতত্ত্বের বিভিন্ন তত্ত্ব ( কার্ল মার্কস , ম্যাক্স ভেবার , ইমিল দার্কহেইম )","বাংলাদেশের সামাজিক কাঠামো ও পরিবর্তন"]},{"name":"বাংলাদেশের অর্থনীতি (Economy of Bangladesh)","chapters":["বাংলাদেশের ভূগোল ও অর্থনৈতিক প্রেক্ষাপট","কৃষি ও শিল্প খাত","অর্থনৈতিক বৃদ্ধি ও উন্নয়ন","শ্রম বাজার ও বেকারত্ব","জাতীয় বাজেট ও কর ব্যবস্থা","ব্যাংকিং ও আর্থিক প্রতিষ্ঠান","বৈদেশিক বাণিজ্য ও রেমিট্যান্স","দারিদ্র্য ও সামাজিক নিরাপত্তা","উন্নয়ন পরিকল্পনা ও নীতি","অবকাঠামো উন্নয়ন","পরিবেশ ও টেকসই উন্নয়ন","বর্তমান অর্থনৈতিক সমস্যা ও সম্ভাবনা"]}],
      "3rd Year":[{"name":"সমাজকর্ম পদ্ধতি : কেসওয়ার্ক ও গ্রুপওয়ার্ক (পেপার কোড : 232101)","chapters":["সমাজকর্ম পদ্ধতির পরিচিতি","কেসওয়ার্ক : ধারণা , উদ্দেশ্য ও বৈশিষ্ট্য","কেসওয়ার্ক প্রক্রিয়া ও ধাপসমূহ","ক্লায়েন্ট ও ক্লায়েন্ট সিস্টেম","গ্রুপওয়ার্ক : ধারণা ও উদ্দেশ্য","গ্রুপ ডায়নামিক্স ও গ্রুপ থেরাপি","গ্রুপ পরিকল্পনা ও বাস্তবায়ন","কেস রেকর্ডিং ও রিপোর্টিং","সমাজকর্ম পদ্ধতির মূল্যায়ন ও সীমাবদ্ধতা","প্রয়োগ ক্ষেত্র ও বাস্তব উদাহরণ"]},{"name":"সমাজকর্মে তাত্ত্বিক প্রয়োগ (পেপার কোড : 232103)","chapters":["সমাজকর্মের তাত্ত্বিক ভিত্তি","মনোবিজ্ঞান সম্পর্কিত তত্ত্ব","সামাজিক তত্ত্ব ও সমাজকর্ম","ব্যক্তিত্ব তত্ত্ব ও বিকাশ তত্ত্ব","সামাজিক পরিবর্তন ও উন্নয়নের তত্ত্ব","সামাজিক ন্যায়বিচার ও মানবাধিকার তত্ত্ব","সমাজকর্মে তত্ত্বের প্রয়োগ পদ্ধতি","তাত্ত্বিক মডেল এবং তাদের ব্যবহার","সামাজিক সমস্যা বিশ্লেষণ তত্ত্ব","বাস্তব সমাজকর্মে তাত্ত্বিক প্রয়োগের চ্যালেঞ্জ"]},{"name":"স্বেচ্ছাসেবী প্রতিষ্ঠান ও এনজিও (পেপার কোড : 232105)","chapters":["স্বেচ্ছাসেবী সংস্থার পরিচিতি ও প্রকারভেদ","এনজিও : সংজ্ঞা , ভূমিকা ও গুরুত্ব","এনজিওর ইতিহাস ও বিকাশ","স্বেচ্ছাসেবী কার্যক্রমের প্রকারভেদ","এনজিওর কার্যক্রম ও কার্যপদ্ধতি","অর্থায়ন ও সম্পদ সংগ্রহ","এনজিও ও সরকারী সহযোগিতা","সমাজকর্মে এনজিওর অবদান","উন্নয়ন কার্যক্রমে এনজিওর ভূমিকা","চ্যালেঞ্জ ও ভবিষ্যৎ সম্ভাবনা"]},{"name":"দরিদ্রদের সাথে কাজ : নীতি ও প্রয়োগ (পেপার কোড : 232107)","chapters":["দরিদ্রত্বের ধারণা ও প্রকারভেদ","দরিদ্রদের জীবনের সমস্যাসমূহ","দরিদ্রদের কল্যাণে নীতিমালা","দরিদ্র বিমোচনের বিভিন্ন পদ্ধতি","দরিদ্রদের জন্য সরকারী ও বেসরকারী উদ্যোগ","সামাজিক নিরাপত্তা ব্যবস্থা","দরিদ্রদের ক্ষমতায়ন ও সামাজিক অন্তর্ভুক্তি","দরিদ্রদের জীবনমান উন্নয়নে সমাজকর্মের ভূমিকা","কার্যকর পরিকল্পনা ও প্রোগ্রাম বাস্তবায়ন","দরিদ্রতা নিরসনে আন্তর্জাতিক সহযোগিতা"]},{"name":"প্রকল্প পরিকল্পনা ও ব্যবস্থাপনা (পেপার কোড : 232109)","chapters":["প্রকল্প পরিকল্পনার ধারণা ও গুরুত্ব","প্রকল্প পরিকল্পনা প্রক্রিয়া","প্রকল্পের লক্ষ্য নির্ধারণ","বাজেট ও সম্পদ পরিকল্পনা","প্রকল্প বাস্তবায়ন ও নিয়ন্ত্রণ","প্রকল্প ব্যবস্থাপনার কৌশল","প্রকল্প মূল্যায়ন ও মনিটরিং","সমস্যা সমাধান ও ঝুঁকি ব্যবস্থাপনা","দল ও নেতৃত্ব উন্নয়ন","প্রকল্পের সফলতা ও ব্যর্থতার কারণ"]},{"name":"জনসংখ্যা ইস্যু , নীতি পরিকল্পনা ও সেবা (পেপার কোড : 232111)","chapters":["জনসংখ্যার ধারণা ও বৈশিষ্ট্য","জনসংখ্যার বৃদ্ধি ও এর প্রভাব","জনসংখ্যা সমস্যা ও সমাধান","জনসংখ্যা নীতি ও পরিকল্পনা","পরিবার পরিকল্পনা ও স্বাস্থ্য সেবা","জনসংখ্যা ও অর্থনৈতিক উন্নয়ন","জনসংখ্যার বয়স্ককরণ ও তার প্রভাব","শিশুসংখ্যা ও শিক্ষা","জনসংখ্যা স্বাস্থ্য সেবা ব্যবস্থাপনা","আন্তর্জাতিক জনসংখ্যা সমস্যা ও উদ্যোগ"]},{"name":"মানবাধিকার , সামাজিক ন্যায়বিচার ও সমাজকর্ম (পেপার কোড : 232113)","chapters":["মানবাধিকার : সংজ্ঞা ও প্রকারভেদ","সামাজিক ন্যায়বিচারের ধারণা","মানবাধিকার ও সমাজকর্মের সম্পর্ক","আন্তর্জাতিক মানবাধিকার আইন ও চুক্তি","মানবাধিকার লঙ্ঘন ও প্রতিকার","সমাজকর্মে ন্যায়বিচারের প্রয়োগ","সমাজে বৈষম্য ও মানবাধিকার","নারী ও শিশু অধিকারের সুরক্ষা","প্রতিবন্ধী ও সংখ্যালঘুর অধিকার","মানবাধিকার সংরক্ষণে এনজিও ও সরকারী ভূমিকা"]},{"name":"অপরাধ ও সমাজ (পেপার কোড : 232115)","chapters":["অপরাধের ধারণা ও প্রকারভেদ","অপ অপরাধের সামাজিক ও মনোবৈজ্ঞানিক কারণ","অপরাধী ব্যক্তিত্ব ও সমাজের প্রভাব","অপ অপরাধের বিচার ব্যবস্থা","অপ অপরাধ প্রতিরোধ ও নিয়ন্ত্রণ ব্যবস্থা","পুনর্বাসন ও সমাজকর্মের ভূমিকা","যুব অপরাধ ও তার প্রতিকার","নারীর বিরুদ্ধে অপরাধ","মাদকাসক্তি ও অপরাধ","আন্তর্জাতিক অপরাধ ও অপরাধ দমন নীতি"]}],
      "4th Year":[{"name":"গ্রামীণ ও শহর সম্প্রদদায় উন্নয়ন (পেপার কোড : 242101)","chapters":["গ্রামীণ ও শহর সম্প্রদায়ের সংজ্ঞা ও বৈশিষ্ট্য","গ্রামীণ উন্নয়নের তাত্ত্বিক ভিত্তি","শহর উন্নয়ন ও নগরায়ন","গ্রামীণ অর্থনীতি ও সামাজিক কাঠামো","স্থানীয় প্রতিষ্ঠান ও নেতৃত্ব","সম্প্রদায় সংগঠন ও সক্ষমতা বৃদ্ধি","পরিকল্পনা ও সম্প্রদায় উন্নয়ন পদ্ধতি","স্থানীয় সরকার ও উন্নয়ন","সামাজিক পরিবর্তন ও প্রযুক্তি গ্রহণ","বাংলাদেশের গ্রামীণ ও শহর উন্নয়নের বাস্তবতা"]},{"name":"জলবায়ু পরিবর্তন : ইস্যু ও দুর্যোগ ব্যবস্থাপনা (পেপার কোড : 242103)","chapters":["জলবায়ু পরিবর্তনের ধারণা ও কারণ","বৈশ্বিক ও স্থানীয় জলবায়ু পরিবর্তন","প্রাকৃতিক দুর্যোগের প্রকারভেদ","দুর্যোগ ব্যবস্থাপনার ধাপ ও কৌশল","ঝুঁকি মূল্যায়ন ও ঝুঁকি ব্যবস্থাপনা","দুর্যোগ প্রস্তুতি ও প্রতিরোধ ব্যবস্থা","দুর্যোগ থেকে পুনর্বাসন ও পুনর্গঠন","জলবায়ু পরিবর্তন ও সামাজিক প্রভাব","পরিবেশ ও টেকসই উন্নয়ন","বাংলাদেশে দুর্যোগ ব্যবস্থাপনা ও চ্যালেঞ্জ"]},{"name":"মানব সম্পদ ব্যবস্থাপনা (পেপার কোড : 242105)","chapters":["মানব সম্পদের ধারণা ও গুরুত্ব","মানব সম্পদ পরিকল্পনা ও বিশ্লেষণ","কর্মসংস্থান ও জনশক্তি উন্নয়ন","প্রশিক্ষণ ও কর্মী উন্নয়ন","কর্মী মূল্যায়ন ও পরিদর্শন","মানব সম্পদ উন্নয়নে প্রণোদনা ও প্রেরণা","কর্মী সম্পর্ক ও সংঘর্ষ ব্যবস্থাপনা","নেতৃত্ব ও পরিচালনায় মানব সম্পদ","মানব সম্পদ উন্নয়নে প্রযুক্তির ব্যবহার","বাংলাদেশে মানব সম্পদ ব্যবস্থাপনার চ্যালেঞ্জ ও সুযোগ"]},{"name":"সামাজিক গবেষণা ও পরিসংখ্যান (পেপার কোড : 242107)","chapters":["সামাজিক গবেষণার উদ্দেশ্য ও প্রকারভেদ","গবেষণা পরিকল্পনা ও নকশা","তথ্য সংগ্রহের পদ্ধতি","নমুনা গ্রহণ ও তথ্য সংগ্রহ","তথ্য বিশ্লেষণ ও উপস্থাপন","গুণগত ও পরিমাণগত গবেষণা পদ্ধতি","পরিসংখ্যানের মৌলিক ধারণা","উপাত্ত বিশ্লেষণের পদ্ধতি ( মাধ্যম , বিভ্রাট , করেলেশন ইত্যাদি )","গবেষণায় নৈতিকতা ও সততা","রিপোর্ট লেখা ও উপস্থাপনা"]},{"name":"বাংলাদেশে সামাজিক সেবা (পেপার কোড : 242109)","chapters":["সামাজিক সেবার ধারণা ও প্রয়োজনীয়তা","সামাজিক সেবার ইতিহাস ও উন্নয়ন","সামাজিক সেবার বিভিন্ন ধরণ","সরকারী ও বেসরকারী সামাজিক সেবা প্রতিষ্ঠান","সামাজিক সেবার কার্যক্রম ও পরিকল্পনা","সামাজিক সেবার জনবল ও প্রশিক্ষণ","সমাজকল্যাণ কার্যক্রম ও প্রভাব","সামাজিক সেবার ও দারিদ্র্য বিমোচন","বাংলাদেশের সামাজিক সেবা নীতি ও আইন","সামাজিক সেবায় চ্যালেঞ্জ ও ভবিষ্যৎ সম্ভাবনা"]},{"name":"সমাজকর্ম ও বৈশ্বিকরণ (পেপার কোড : 242111)","chapters":["বৈশ্বিকরণ : ধারণা ও প্রভাব","বৈশ্বিক অর্থনীতি ও সামাজিক পরিবর্তন","বৈশ্বিক সংস্থা ও তাদের ভূমিকা","বৈশ্বিকায়নে সমাজকর্মের প্রয়োজনীয়তা","বৈশ্বিক মানবাধিকার ও সামাজিক ন্যায়বিচার","বৈশ্বিক সংকট ও দুর্যোগ ব্যবস্থাপনা","বৈশ্বিকায়ন ও স্থানীয় সম্প্রদায়","সমাজকর্মে প্রযুক্তির ব্যবহার","আন্তর্জাতিক উন্নয়ন সহযোগিতা","বাংলাদেশে বৈশ্বিকায়নের প্রভাব ও সমাজকর্ম"]},{"name":"জনস্বাস্থ্য ও সমাজকর্ম (পেপার কোড : 232113 / 242113)","chapters":["জনস্বাস্থ্যের পরিচিতি ও গুরুত্ব","স্বাস্থ্য ও সামাজিক determinants","রোগ প্রতিরোধ ও স্বাস্থ্যসেবা ব্যবস্থা","জনস্বাস্থ্য পরিকল্পনা ও নীতি","সমাজকর্মের মাধ্যমে স্বাস্থ্যসেবা প্রদান","স্বাস্থ্য শিক্ষা ও সম্প্রদায় ভিত্তিক উদ্যোগ","শিশু , নারী ও বৃদ্ধ স্বাস্থ্য","মানসিক স্বাস্থ্য ও সমাজকর্ম","স্বাস্থ্য সম্পর্কিত সামাজিক সমস্যা","বাংলাদেশের জনস্বাস্থ্য : চ্যালেঞ্জ ও উন্নয়ন"]},{"name":"সামাজিক কর্ম , আইন ও সমাজকর্ম (পেপার কোড : 242115)","chapters":["সামাজিক কর্মের ধারণা ও প্রকারভেদ","সামাজিক কর্ম ও আইন : সম্পর্ক ও প্রভাব","মানবাধিকার ও আইন","সামাজিক নীতি ও আইন প্রণয়ন","সমাজকর্মে আইনি সাহায্যের ভূমিকা","দণ্ডবিধি ও সমাজকর্ম","শিশু অধিকার আইন ও সমাজকর্ম","নারীর বিরুদ্ধে সহিংসতা ও আইন","আইনগত সেবা প্রদান পদ্ধতি","বাংলাদেশের সামাজিক কর্ম ও আইনগত কাঠামো"]},{"name":"মাঠকর্ম শিক্ষা (Field Work Education) (পেপার কোড : 242117)","chapters":["মাঠকর্মের উদ্দেশ্য ও গুরুত্ব","মাঠকর্ম পরিকল্পনা ও প্রস্তুতি","ক্লায়েন্ট সিস্টেম চিহ্নিতকরণ","তথ্য সংগ্রহ ও মূল্যায়ন","পরিষেবা প্রদান ও পরিচালনা","দল ও সুপারভিশন","প্রতিবেদন লেখা ও উপস্থাপন","নৈতিকতা ও পেশাগত দায়িত্ব","সমস্যা সমাধান ও সিদ্ধান্ত গ্রহণ","মাঠকর্মের মূল্যায়ন ও প্রতিক্রিয়া"]},{"name":"মাঠকর্ম কার্যক্রম ও ভাইভা (Field Practicum + Viva Voce) (পেপার কোড : 242118)","chapters":["মাঠকর্ম কার্যক্রমের বাস্তবায়ন","ক্লায়েন্ট ও সম্প্রদায়ের সাথে কাজ","মাঠকর্ম প্রতিবেদন প্রস্তুতি","সমস্যা ও সমাধানের বিশ্লেষণ","সুপারভাইজার ও পিয়ার ফিডব্যাক","ভাইভা প্রস্তুতি ও প্রশ্নোত্তর","পেশাগত আচরণ ও মূল্যায়ন","কাজের নৈতিকতা ও সামাজিক দায়বদ্ধতা","ক্ষেত্র অভিজ্ঞতা থেকে শিক্ষা","ভবিষ্যত পরিকল্পনা ও দিকনির্দেশনা"]}],
      "Others":[{"name":"ইংরেজি (English)","chapters":["Grammar: Parts of Speech","Sentence Structure ও Types of Sentences","Tenses (Present, Past, Future)","Active and Passive Voice","Direct and Indirect Speech","Articles, Prepositions, Conjunctions","Vocabulary Development (Synonyms, Antonyms, Homonyms)","Composition Writing (Paragraph, Letter Writing)","Comprehension (Reading and Understanding passages)","Translation ( বাংলা থেকে ইংরেজি )","Essay Writing (Narrative, Descriptive, Argumentative)","Precis Writing","Letter Writing (Formal and Informal)","Report Writing","Dialogue Writing","Reading Comprehension (Unseen passages)","Literature: Short Stories / Poems / Drama (Selected texts)","Use of Idioms and Phrases","Summary Writing","Translation ( ইংরেজি থেকে বাংলা )"]}]
    };

    // --- Helpers ---
    function debounce(fn, delay = 300) {
      let t; return (...args) => { clearTimeout(t); t = setTimeout(() => fn(...args), delay); };
    }

    async function fetchWithRetry(url, options) {
      const maxRetries = 3; let delay = 1000;
      for (let i = 0; i < maxRetries; i++) {
        try {
          const res = await fetch(url, options);
          if (!res.ok) {
             const errText = await res.text();
             throw new Error(`API Error: ${res.status} ${errText}`);
          }
          return res;
        } catch (e) {
          if (i === maxRetries - 1) throw e;
          await new Promise(r => setTimeout(r, delay)); delay *= 2;
        }
      }
    }

    function toast(message, type = 'info') {
      const container = document.getElementById('toast-container');
      const el = document.createElement('div');
      const baseClass = "transform transition-all duration-300 ease-out translate-y-2 opacity-0 flex items-center gap-3 shadow-xl rounded-xl px-5 py-4 max-w-sm border-l-4";
      let colors = type === 'error' ? "bg-white text-gray-800 border-red-500" : (type === 'success' ? "bg-white text-gray-800 border-green-500" : "bg-gray-800 text-white border-gray-600");
      el.className = `${baseClass} ${colors}`;
      el.innerHTML = `<p class="text-sm font-medium">${message}</p>`;
      container.appendChild(el);
      requestAnimationFrame(() => el.classList.remove('translate-y-2', 'opacity-0'));
      setTimeout(() => { el.classList.add('translate-y-2', 'opacity-0'); setTimeout(() => container.removeChild(el), 300); }, 3000);
    }

    async function copyToClipboard(text) {
      try { await navigator.clipboard.writeText(text); toast('কপি করা হয়েছে', 'success'); } catch { toast('কপি করা যায়নি', 'error'); }
    }

    function updateSavedCountBadge() {
      const badge = document.getElementById('saved-count-badge');
      const saved = JSON.parse(localStorage.getItem(LS_KEY_SAVED)) || [];
      badge.textContent = saved.length;
      badge.classList.remove('scale-125'); requestAnimationFrame(() => badge.classList.add('scale-100')); 
    }

    // --- DOM Elements ---
    const generateBtn = document.getElementById('generate-btn');
    const btnText = document.getElementById('btn-text');
    const loader = document.getElementById('loader');
    const resultContainer = document.getElementById('result-container');
    const yearSelect = document.getElementById('year-select');
    const subjectSelect = document.getElementById('subject-select');
    const chapterSelect = document.getElementById('chapter-select');

    // Tabs
    const tabBtns = { 
      generate: document.getElementById('tab-btn-generate'), 
      tracker: document.getElementById('tab-btn-tracker'),
      collection: document.getElementById('tab-btn-collection') 
    };
    const tabContents = { 
      generate: document.getElementById('tab-content-generate'), 
      tracker: document.getElementById('tab-content-tracker'),
      collection: document.getElementById('tab-content-collection') 
    };
    const collectionContainer = document.getElementById('collection-container');

    // --- Tab Logic ---
    function switchTab(activeTabName) {
      Object.keys(tabBtns).forEach(name => {
        const btn = tabBtns[name];
        if (name === activeTabName) {
          btn.classList.add('tab-active', 'text-primary-700', 'bg-primary-50'); btn.classList.remove('tab-inactive', 'text-gray-500'); btn.setAttribute('aria-selected', 'true');
        } else {
          btn.classList.remove('tab-active', 'text-primary-700', 'bg-primary-50'); btn.classList.add('tab-inactive', 'text-gray-500'); btn.setAttribute('aria-selected', 'false');
        }
      });
      Object.keys(tabContents).forEach(name => {
        const content = tabContents[name];
        if (name === activeTabName) {
          content.classList.remove('hidden'); content.classList.add('animate-slide-up');
        } else {
          content.classList.add('hidden'); content.classList.remove('animate-slide-up');
        }
      });
      if (activeTabName === 'collection') renderSavedQuestions();
      if (activeTabName === 'tracker') initTracker();
    }
    Object.keys(tabBtns).forEach(name => { tabBtns[name].addEventListener('click', () => switchTab(name)); });
    document.getElementById('header-saved-btn')?.addEventListener('click', () => switchTab('collection'));

    // --- Dropdown Logic ---
    function populateYears() {
      Object.keys(subjectData).forEach(year => {
        const bn = year === '1st Year' ? '১ম বর্ষ' : year === '2nd Year' ? '২য় বর্ষ' : year === '3rd Year' ? '৩য় বর্ষ' : year === '4th Year' ? '৪র্থ বর্ষ' : 'অন্যান্য';
        yearSelect.add(new Option(`${year} (${bn})`, year));
      });
    }
    function populateSubjects(year) {
      subjectSelect.innerHTML = '<option value=""> </option>'; chapterSelect.innerHTML = '<option value=""> </option>';
      subjectSelect.disabled = true; chapterSelect.disabled = true; generateBtn.disabled = true;
      if(year) { subjectData[year].forEach(s => subjectSelect.add(new Option(s.name, s.name))); subjectSelect.disabled = false; }
    }
    function populateChapters(year, subjectName) {
      chapterSelect.innerHTML = '<option value=""> </option>'; chapterSelect.disabled = true; generateBtn.disabled = true;
      if(year && subjectName) {
        const sub = subjectData[year].find(s => s.name === subjectName);
        if(sub) { sub.chapters.forEach(c => chapterSelect.add(new Option(c, c))); chapterSelect.disabled = false; }
      }
    }
    yearSelect.addEventListener('change', () => { populateSubjects(yearSelect.value); localStorage.setItem(LS_KEY_YEAR, yearSelect.value); });
    subjectSelect.addEventListener('change', () => { populateChapters(yearSelect.value, subjectSelect.value); localStorage.setItem(LS_KEY_SUBJECT, subjectSelect.value); });
    chapterSelect.addEventListener('change', () => { const val = chapterSelect.value; generateBtn.disabled = !val; if(val) localStorage.setItem(LS_KEY_CHAPTER, val); });

    // --- Load State ---
    window.addEventListener('DOMContentLoaded', () => {
      populateYears();
      const sYear = localStorage.getItem(LS_KEY_YEAR);
      if(sYear) {
        yearSelect.value = sYear; populateSubjects(sYear);
        const sSub = localStorage.getItem(LS_KEY_SUBJECT);
        if(sSub && subjectSelect.querySelector(`option[value="${sSub}"]`)) {
          subjectSelect.value = sSub; populateChapters(sYear, sSub);
          const sChap = localStorage.getItem(LS_KEY_CHAPTER);
          if(sChap && chapterSelect.querySelector(`option[value="${sChap}"]`)) { chapterSelect.value = sChap; generateBtn.disabled = false; }
        }
      }
      const genHtml = localStorage.getItem(LS_KEY_QUESTION_GEN);
      if(genHtml && genHtml.includes('card')) resultContainer.innerHTML = genHtml;
      
      updateSavedCountBadge();
    });

    // --- SYLLABUS TRACKER LOGIC ---
    function initTracker() {
      const yearFilter = document.getElementById('tracker-year-filter');
      if (yearFilter.options.length === 0) {
        Object.keys(subjectData).forEach(year => {
           const bn = year === '1st Year' ? '১ম বর্ষ' : year === '2nd Year' ? '২য় বর্ষ' : year === '3rd Year' ? '৩য় বর্ষ' : year === '4th Year' ? '৪র্থ বর্ষ' : 'অন্যান্য';
           yearFilter.add(new Option(`${year} (${bn})`, year));
        });
        const savedYear = localStorage.getItem(LS_KEY_YEAR) || '1st Year';
        yearFilter.value = savedYear;
        yearFilter.addEventListener('change', () => renderTrackerList(yearFilter.value));
      }
      renderTrackerList(yearFilter.value);
    }

    function renderTrackerList(selectedYear) {
      const container = document.getElementById('tracker-list-container');
      const subjects = subjectData[selectedYear] || [];
      const completedSet = new Set(JSON.parse(localStorage.getItem(LS_KEY_SYLLABUS)) || []);
      
      let totalChapters = 0;
      let completedChapters = 0;
      let html = '';
      
      if(subjects.length === 0) {
          container.innerHTML = '<div class="text-center text-gray-500 py-10">কোনো তথ্য পাওয়া যায়নি</div>';
          return;
      }

      subjects.forEach((sub, sIdx) => {
        let subCompletedCount = 0;
        const chaptersHtml = sub.chapters.map((chap, cIdx) => {
          const id = `${selectedYear}|${sub.name}|${chap}`;
          const isChecked = completedSet.has(id);
          totalChapters++;
          if(isChecked) { completedChapters++; subCompletedCount++; }
          return `
            <label class="checkbox-wrapper flex items-start gap-3 p-3 hover:bg-gray-50 rounded-lg cursor-pointer group transition-colors">
              <input type="checkbox" class="hidden" onchange="toggleChapter('${id}')" ${isChecked ? 'checked' : ''}>
              <div class="w-5 h-5 border-2 border-gray-300 rounded flex items-center justify-center transition-all bg-white group-hover:border-primary-400 mt-0.5">
                <svg class="w-3 h-3 text-white hidden pointer-events-none" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M5 13l4 4L19 7"></path></svg>
              </div>
              <span class="text-sm text-gray-700 ${isChecked ? 'line-through text-gray-400' : ''}">${chap}</span>
            </label>
          `;
        }).join('');

        const progressPercent = Math.round((subCompletedCount / sub.chapters.length) * 100) || 0;
        html += `
          <div class="bg-white border border-gray-100 rounded-2xl overflow-hidden shadow-sm">
            <div class="p-4 bg-gray-50 border-b border-gray-100 flex justify-between items-center cursor-pointer hover:bg-gray-100 transition-colors" onclick="this.nextElementSibling.classList.toggle('hidden'); this.querySelector('.arrow').classList.toggle('rotate-180')">
              <div class="flex-1">
                 <h3 class="font-bold text-gray-800 text-sm md:text-base">${sub.name}</h3>
                 <div class="flex items-center gap-2 mt-1">
                   <div class="w-24 bg-gray-200 rounded-full h-1.5"><div class="bg-primary-500 h-1.5 rounded-full" style="width: ${progressPercent}%"></div></div>
                   <span class="text-xs text-gray-500">${subCompletedCount}/${sub.chapters.length}</span>
                 </div>
              </div>
              <svg class="arrow w-5 h-5 text-gray-400 transition-transform duration-300 transform rotate-180" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path></svg>
            </div>
            <div class="">
               <div class="p-2 grid grid-cols-1 md:grid-cols-2 gap-0">
                 ${chaptersHtml}
               </div>
            </div>
          </div>
        `;
      });

      container.innerHTML = html;
      updateTrackerDashboard(selectedYear, completedChapters, totalChapters);
    }

    function toggleChapter(id) {
      const completedSet = new Set(JSON.parse(localStorage.getItem(LS_KEY_SYLLABUS)) || []);
      if (completedSet.has(id)) completedSet.delete(id);
      else completedSet.add(id);
      localStorage.setItem(LS_KEY_SYLLABUS, JSON.stringify([...completedSet]));
      const yearFilter = document.getElementById('tracker-year-filter');
      renderTrackerList(yearFilter.value);
    }

    function updateTrackerDashboard(year, completed, total) {
      const percentage = total === 0 ? 0 : Math.round((completed / total) * 100);
      const bnYear = year === '1st Year' ? '১ম বর্ষের' : year === '2nd Year' ? '২য় বর্ষের' : year === '3rd Year' ? '৩য় বর্ষের' : year === '4th Year' ? '৪র্থ বর্ষের' : 'অন্যান্য';
      document.getElementById('tracker-summary-text').textContent = `আপনার ${bnYear} প্রস্তুতি সম্পন্ন হয়েছে`;
      document.getElementById('tracker-percent').textContent = `${percentage}%`;
      document.getElementById('tracker-progress-bar').style.width = `${percentage}%`;
    }

    // --- Render Logic (Card Design) ---
    function renderCard(q, index, type) {
       let badges = ``;
       if (q.importance === 'High') badges += `<span class="bg-yellow-100 text-yellow-700 text-xs px-2 py-0.5 rounded-full font-bold flex items-center gap-1">⚡ High Priority</span>`;
       if (q.difficulty) badges += `<span class="bg-gray-100 text-gray-600 text-xs px-2 py-0.5 rounded-full">${q.difficulty}</span>`;
       
       const isSaved = (JSON.parse(localStorage.getItem(LS_KEY_SAVED))||[]).some(sq => sq.question === q.question);
       const btnClass = isSaved ? 'bg-green-100 text-green-700' : 'bg-gray-100 hover:bg-gray-200 text-gray-600';
       const btnIcon = isSaved ? 
         `<svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20"><path d="M5 4a2 2 0 012-2h6a2 2 0 012 2v14l-5-2.5L5 18V4z"></path></svg>` : 
         `<svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 5a2 2 0 012-2h10a2 2 0 012 2v16l-7-3.5L5 21V5z"></path></svg>`;

       let optionsHtml = '';
       if(q.type === 'MCQ' && q.options) {
         optionsHtml = `<div class="grid grid-cols-1 md:grid-cols-2 gap-2 mt-3">
           ${q.options.map(opt => `
             <div class="p-3 rounded-lg border border-gray-100 text-sm ${opt === q.correct_answer ? 'bg-green-50 border-green-200 text-green-800 font-medium' : 'bg-gray-50 text-gray-600'}">
               ${opt} ${opt === q.correct_answer ? '✅' : ''}
             </div>
           `).join('')}
         </div>`;
       }
       return `
         <div class="bg-white rounded-2xl p-5 md:p-6 shadow-sm hover:shadow-md transition-shadow duration-300 border border-primary-50 relative group animate-fade-in card">
           <div class="flex justify-between items-start gap-4 mb-3">
             <div class="flex-1">
               <div class="flex flex-wrap gap-2 mb-2">
                 <span class="text-xs font-bold text-primary-600 uppercase tracking-wider">প্রশ্ন ${index+1}</span>
                 ${badges}
               </div>
               <h3 class="text-lg md:text-xl font-content font-medium text-gray-800 leading-relaxed">${q.question}</h3>
             </div>
             <button onclick="${type === 'saved' ? `removeSavedQuestion(this, ${index})` : 'saveQuestion(this)'}" 
                     data-question='${JSON.stringify(q).replace(/'/g, "&#39;")}'
                     class="p-2 rounded-xl transition-all ${type === 'saved' ? 'bg-red-50 text-red-500 hover:bg-red-100' : btnClass}" 
                     title="${type === 'saved' ? 'Remove' : 'Save'}">
                ${type === 'saved' ? '<svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path></svg>' : btnIcon}
             </button>
           </div>
           ${optionsHtml}
           <div class="mt-4 pt-4 border-t border-gray-50 flex items-center justify-between">
             <button onclick="toggleAnswer(this)" class="text-sm font-semibold text-primary-600 hover:text-primary-700 flex items-center gap-1 focus:outline-none">
               <span>উত্তর দেখুন</span>
               <svg class="w-4 h-4 transition-transform transform" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path></svg>
             </button>
             <div class="flex gap-2 items-center">
               <button onclick="askAiQuestion('${q.question}')" class="text-xs flex items-center gap-1 bg-gradient-to-r from-primary-500 to-primary-600 text-white px-2 py-1 rounded-md hover:shadow-md transition-all">
                 <svg class="w-3 h-3" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"></path></svg>
                 AI Ask
               </button>
               <span class="text-xs text-gray-400 bg-gray-50 px-2 py-1 rounded-md">${q.type}</span>
             </div>
           </div>
           <div class="answer-content mt-2 bg-slate-50 rounded-xl">
             <div class="p-4 text-gray-700 text-sm md:text-base leading-relaxed font-content">
               <div class="flex justify-between mb-2">
                 <strong class="text-gray-900 block">মডেল উত্তর:</strong>
                 <button class="text-xs text-gray-400 hover:text-primary-600 copy-btn">Copy</button>
               </div>
               <div class="whitespace-pre-wrap answer-text">${q.model_answer || 'N/A'}</div>
               <div class="mt-3 text-xs text-gray-500 pt-2 border-t border-gray-200">
                 <span class="font-bold">তথ্যসূত্র:</span> ${q.references ? q.references.join(', ') : 'N/A'}
               </div>
             </div>
           </div>
         </div>
       `;
    }

    function toggleAnswer(btn) {
      const content = btn.parentElement.nextElementSibling;
      const arrow = btn.querySelector('svg');
      const isShow = content.classList.contains('show');
      if(isShow) {
        content.classList.remove('show'); btn.querySelector('span').textContent = 'উত্তর দেখুন'; arrow.classList.remove('rotate-180');
      } else {
        content.classList.add('show'); btn.querySelector('span').textContent = 'উত্তর লুকান'; arrow.classList.add('rotate-180');
      }
    }

    function renderQuestions(questions, containerId) {
      const container = document.getElementById(containerId);
      if(!questions || !questions.length) return;
      container.innerHTML = questions.map((q, i) => renderCard(q, i, containerId === 'collection-container' ? 'saved' : 'gen')).join('');
      container.querySelectorAll('.copy-btn').forEach(btn => {
        btn.addEventListener('click', (e) => {
          const text = e.target.closest('.answer-content').querySelector('.answer-text').innerText; copyToClipboard(text);
        });
      });
      if(containerId === 'result-container') localStorage.setItem(LS_KEY_QUESTION_GEN, container.innerHTML);
    }

    function saveQuestion(btn) {
      try {
        const q = JSON.parse(btn.getAttribute('data-question'));
        let saved = JSON.parse(localStorage.getItem(LS_KEY_SAVED)) || [];
        if(!saved.some(sq => sq.question === q.question)) {
          saved.push(q); localStorage.setItem(LS_KEY_SAVED, JSON.stringify(saved));
          updateSavedCountBadge(); toast('প্রশ্নটি সংগ্রহে রাখা হয়েছে', 'success');
          btn.innerHTML = `<svg class="w-5 h-5 text-green-600" fill="currentColor" viewBox="0 0 20 20"><path d="M5 4a2 2 0 012-2h6a2 2 0 012 2v14l-5-2.5L5 18V4z"></path></svg>`;
          btn.classList.remove('bg-gray-100', 'text-gray-600'); btn.classList.add('bg-green-100', 'text-green-600');
        } else { toast('ইতিমধ্যে সংগ্রহে আছে', 'info'); }
      } catch(e) { console.error(e); }
    }

    function removeSavedQuestion(btn, index) {
      let saved = JSON.parse(localStorage.getItem(LS_KEY_SAVED)) || [];
      saved.splice(index, 1); localStorage.setItem(LS_KEY_SAVED, JSON.stringify(saved));
      renderSavedQuestions(); toast('মুছে ফেলা হয়েছে', 'success');
    }

    function renderSavedQuestions() {
      const saved = JSON.parse(localStorage.getItem(LS_KEY_SAVED)) || [];
      updateSavedCountBadge();
      if(!saved.length) {
        collectionContainer.innerHTML = `<div class="text-center py-10 text-gray-400"><div class="bg-primary-50 w-16 h-16 rounded-full flex items-center justify-center mx-auto mb-3"><svg class="w-8 h-8 opacity-50" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 19a2 2 0 01-2-2V7a2 2 0 012-2h4l2 2h4a2 2 0 012 2v1M5 19h14a2 2 0 002-2v-5a2 2 0 00-2-2H9a2 2 0 00-2 2v5a2 2 0 01-2 2z"></path></svg></div><p>আপনার সংগ্রহে কোনো প্রশ্ন নেই</p></div>`;
        return;
      }
      renderQuestions(saved, 'collection-container');
    }

    // --- PDF Download Functionality (UPDATED HIGH QUALITY A4 & NO CUTTING) ---
    document.getElementById('download-pdf-btn').addEventListener('click', () => {
        const element = document.getElementById('collection-container');
        const saved = JSON.parse(localStorage.getItem(LS_KEY_SAVED)) || [];
        
        if (!saved.length) {
            toast('ডাউনলোড করার জন্য কোনো প্রশ্ন নেই', 'info');
            return;
        }

        toast('PDF তৈরি হচ্ছে... অনুগ্রহ করে অপেক্ষা করুন', 'info');

        // Temporarily expand all answers and adjust styles for printing
        const answers = element.querySelectorAll('.answer-content');
        answers.forEach(el => {
            el.classList.add('show');
            el.style.maxHeight = 'none'; 
            el.parentElement.style.color = '#000000'; // Ensure high contrast
        });

        const opt = {
            margin:       0.5,
            filename:     'NU_Smart_Question_Bank.pdf',
            image:        { type: 'jpeg', quality: 0.98 },
            // Scale reduced to 2 for better stability, but still high quality for print
            html2canvas:  { scale: 2, useCORS: true, scrollY: 0 },
            jsPDF:        { unit: 'in', format: 'a4', orientation: 'portrait' },
            // Important: This prevents text cutting
            pagebreak:    { mode: ['avoid-all', 'css', 'legacy'] } 
        };

        html2pdf().set(opt).from(element).save().then(() => {
            // Restore original state
            answers.forEach(el => {
                el.classList.remove('show');
                el.style.maxHeight = null; 
                el.parentElement.style.color = '';
            });
            toast('PDF ডাউনলোড সম্পন্ন হয়েছে', 'success');
        });
    });

    document.getElementById('export-csv-btn').addEventListener('click', () => {
      const saved = JSON.parse(localStorage.getItem(LS_KEY_SAVED)) || [];
      if (!saved.length) return toast('রপ্তানির জন্য কিছু নেই', 'info');
      const csvContent = "data:text/csv;charset=utf-8," + "Question,Type,Answer\n" + saved.map(e => `"${e.question.replace(/"/g, '""')}","${e.type}","${(e.model_answer||'').replace(/"/g, '""')}"`).join("\n");
      const encodedUri = encodeURI(csvContent);
      const link = document.createElement("a"); link.setAttribute("href", encodedUri); link.setAttribute("download", "my_questions.csv"); document.body.appendChild(link); link.click(); document.body.removeChild(link);
    });
    document.getElementById('clear-all-btn').addEventListener('click', () => {
      if(confirm('আপনি কি নিশ্চিত যে সব মুছে ফেলতে চান?')) { localStorage.removeItem(LS_KEY_SAVED); renderSavedQuestions(); toast('সব মুছে ফেলা হয়েছে', 'success'); }
    });

    // --- AI Prompts (Robust Version) ---
    function getAiPrompt(subject, chapter, topic, type, count) {
      let qType = (type === 'Mixed') ? `Generate ${count} questions. Create a mix of question types (MCQ, Short Answer, Long Answer).` : `Generate ${count} ${type} questions.`;
      return `
You are an expert academic assistant for National University (NU), Bangladesh.
Subject: ${subject}
Chapter: ${chapter}
Task: ${qType}

Generate a structured JSON output. The root must be a JSON object containing a single key "questions", which is a list of question objects.
DO NOT output markdown. Output ONLY raw JSON.

Structure per question:
{
  "question": "Question text in Bengali",
  "type": "MCQ | Short | Long",
  "difficulty": "Easy | Medium | Hard",
  "importance": "High | Medium | Low",
  "options": ["Option A", "Option B", "Option C", "Option D"] (Only for MCQ),
  "correct_answer": "Option B" (Only for MCQ),
  "model_answer": "Detailed model answer in Bengali",
  "references": ["Previous Year Question", "Textbook"]
}
`;
    }

    // --- AI Action Listeners ---
    generateBtn.addEventListener('click', async () => {
      const sub = subjectSelect.value; const chap = chapterSelect.value;
      if(!sub || !chap) return toast('বিষয় ও অধ্যায় নির্বাচন করুন', 'error');
      
      btnText.textContent = 'তৈরি হচ্ছে...'; loader.classList.remove('hidden'); generateBtn.disabled = true;
      resultContainer.innerHTML = `<div class="flex justify-center py-12"><div class="dots-loader"><div class="dot1"></div><div class="dot2"></div><div class="dot3"></div></div></div>`;
      
      try {
        const prompt = getAiPrompt(sub, chap, chap, document.getElementById('type').value, document.getElementById('question-count').value);
        const res = await fetchWithRetry(apiUrl, {
          method: 'POST', headers: {'Content-Type': 'application/json'},
          body: JSON.stringify({ contents: [{ parts: [{ text: prompt }] }] })
        });
        const data = await res.json();
        const text = data.candidates?.[0]?.content?.parts?.[0]?.text || '';
        const cleaned = text.replace(/```json/g, '').replace(/```/g, '').trim();
        const json = JSON.parse(cleaned);
        renderQuestions(json.questions, 'result-container');
        toast('প্রশ্ন তৈরি সম্পন্ন!', 'success');
      } catch(e) {
        console.error(e);
        resultContainer.innerHTML = `<div class="text-red-500 text-center p-4">দুঃখিত, কোনো ত্রুটি হয়েছে। (${e.message})</div>`;
        toast('ত্রুটি হয়েছে', 'error');
      } finally {
        btnText.textContent = 'প্রশ্ন জেনারেট করুন'; loader.classList.add('hidden'); generateBtn.disabled = false;
      }
    });

    // --- Chat Widget Logic (Updated with Memory & Persona) ---
    const chatFab = document.getElementById('chat-fab');
    const chatWidget = document.getElementById('chat-widget');
    const closeChatBtn = document.getElementById('close-chat-btn');
    const sendMsgBtn = document.getElementById('send-msg-btn');
    const chatInput = document.getElementById('chat-input');
    const chatBody = document.getElementById('chat-body');

    // Conversation History Storage
    let chatHistory = [];

    // System Instruction with Persona & Academic Rules
    const systemPromptText = `
আপনি জাতীয় বিশ্ববিদ্যালয় (NU), বাংলাদেশের সমাজকর্ম বিভাগের শিক্ষার্থীদের জন্য একটি দক্ষ 'AI স্টাডি অ্যাসিস্ট্যান্ট'। সর্বদা বাংলায় উত্তর দিবেন।

***ব্যক্তিত্ব ও পরিচয় নির্দেশাবলী (Persona Rules) - গুরুত্ব সহকারে পালন করুন:***
১. ব্যবহারকারী যদি প্রশ্ন করে আপনাকে কে তৈরি করেছে বা আপনার নির্মাতা কে (Who created you?), তবে ঠিক এই উত্তরটি দিবেন:
   "আমাকে শাহাদৎ হোসেন নামে একজন শিক্ষার্থী তৈরি করেছেন। আপনি কি তার সাথে যোগাযোগ করতে চান?"
২. যদি ব্যবহারকারী যোগাযোগ করতে চায় বা 'হ্যাঁ' (Yes) বলে, তবেই এই ফোন নাম্বারটি দিবেন: **01760750263**।
৩. যদি ব্যবহারকারী যোগাযোগ করতে না চায়, তবে অন্য প্রসঙ্গে চলে যাবেন।

***অ্যাকাডেমিক নির্দেশাবলী:***
ব্যবহারকারীর ইনপুট বিশ্লেষণ করুন। যদি এটি সাধারণ কুশল বিনিময় হয়, স্বাভাবিক উত্তর দিন। যদি এটি অ্যাকাডেমিক প্রশ্ন হয়, তবে নিচের নিয়ম মানুন:

১. জ্ঞানমূলক প্রশ্ন (Knowledge-based):
- সংজ্ঞা ও তথ্য সরাসরি দিন। পয়েন্ট আকারে ছোট বাক্যে সাজান। একটি ছোট উদাহরণ দিন।

২. অনুধাবন/সংক্ষিপ্ত প্রশ্ন (Short):
- ভূমিকা: ১–২ লাইনে। মূল আলোচনা: ৩–৪টি পয়েন্টে। মোট ৮–১০ লাইন।

৩. রচনামূলক/লম্বা প্রশ্ন (Long):
- ভূমিকা: ৪–৫ লাইন। মূল অংশ: ৫–৭টি পয়েন্টে বিস্তারিত (তত্ত্ব ও উদাহরণসহ)। উপসংহার: ২–৩ লাইন। শিরোনাম ও বুলেট ব্যবহার করুন।

৪. সাধারণ নির্দেশনা:
- প্রশ্নের কীওয়ার্ড আন্ডারলাইন বা Bold করুন। বাংলাদেশি প্রেক্ষাপট ও উদাহরণ দিন।
`;

    // New Function to Ask AI Directly
    function askAiQuestion(questionText) {
        // Force open chat if closed
        const isClosed = chatWidget.classList.contains('chat-closed');
        if (isClosed) {
            toggleChat();
        }
        
        // Prepare prompt
        const prompt = `এই প্রশ্নটি বিস্তারিত বুঝিয়ে বল: "${questionText}"`;
        chatInput.value = prompt;
        
        // Small delay to ensure UI updates before sending
        setTimeout(() => {
            handleSend();
        }, 300);
    }

    function toggleChat() {
      const isClosed = chatWidget.classList.contains('chat-closed');
      if (isClosed) { 
          chatWidget.classList.remove('chat-closed'); 
          chatWidget.classList.add('chat-open'); 
          if(window.innerWidth < 768) document.body.style.overflow = 'hidden';
          setTimeout(() => chatInput.focus(), 100); 
      } 
      else { 
          chatWidget.classList.remove('chat-open'); 
          chatWidget.classList.add('chat-closed'); 
          document.body.style.overflow = '';
      }
    }
    chatFab.addEventListener('click', toggleChat);
    closeChatBtn.addEventListener('click', toggleChat);

    function appendMessage(text, sender) {
      const div = document.createElement('div');
      div.className = `flex gap-3 items-start ${sender === 'user' ? 'flex-row-reverse' : ''}`;
      const avatar = sender === 'ai' 
        ? `<div class="w-8 h-8 rounded-full bg-primary-100 border border-primary-200 flex items-center justify-center text-primary-600 text-xs font-bold flex-shrink-0">AI</div>`
        : `<div class="w-8 h-8 rounded-full bg-gray-200 flex items-center justify-center text-gray-600 text-xs font-bold flex-shrink-0">ME</div>`;
      const bubbleClass = sender === 'ai' 
        ? 'bg-white text-gray-700 border border-gray-100 rounded-2xl rounded-tl-none chat-bubble-ai' 
        : 'bg-primary-600 text-white rounded-2xl rounded-tr-none shadow-md';
      
      let formattedText = text;
      if (sender === 'ai') {
        formattedText = formattedText.replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>');
        formattedText = formattedText.replace(/\n/g, '<br>');
      }

      div.innerHTML = `${avatar}<div class="${bubbleClass} p-3.5 shadow-sm text-sm leading-relaxed max-w-[80%]">${formattedText}</div>`;
      chatBody.appendChild(div); chatBody.scrollTop = chatBody.scrollHeight;
    }

    function appendLoading() {
      const div = document.createElement('div'); div.id = 'chat-loading'; div.className = 'flex gap-3 items-start';
      div.innerHTML = `<div class="w-8 h-8 rounded-full bg-primary-100 border border-primary-200 flex items-center justify-center text-primary-600 text-xs font-bold flex-shrink-0">AI</div><div class="bg-white p-3.5 rounded-2xl rounded-tl-none shadow-sm border border-gray-100 flex items-center h-10"><div class="typing-dot"></div><div class="typing-dot"></div><div class="typing-dot"></div></div>`;
      chatBody.appendChild(div); chatBody.scrollTop = chatBody.scrollHeight;
    }
    function removeLoading() { document.getElementById('chat-loading')?.remove(); }

    async function handleSend() {
      const text = chatInput.value.trim(); if (!text) return;
      
      // 1. Display User Message
      appendMessage(text, 'user'); 
      chatInput.value = ''; 
      appendLoading();

      // 2. Update History
      chatHistory.push({ role: "user", parts: [{ text: text }] });

      try {
        // 3. Send History + System Instruction to API
        const payload = {
            contents: chatHistory,
            systemInstruction: {
                parts: [{ text: systemPromptText }]
            }
        };

        const res = await fetchWithRetry(apiUrl, {
          method: 'POST', 
          headers: {'Content-Type': 'application/json'},
          body: JSON.stringify(payload)
        });
        
        const data = await res.json();
        const reply = data.candidates?.[0]?.content?.parts?.[0]?.text || 'দুঃখিত, উত্তর দেওয়া সম্ভব হচ্ছে না।';
        
        // 4. Update History with Model Response
        chatHistory.push({ role: "model", parts: [{ text: reply }] });

        // 5. Display AI Response
        removeLoading(); 
        appendMessage(reply, 'ai');

      } catch (e) {
        removeLoading(); 
        appendMessage("দুঃখিত, একটি ত্রুটি হয়েছে।", 'ai'); 
        console.error(e);
        // Remove failed user message from history to prevent sync issues
        chatHistory.pop();
      }
    }

    sendMsgBtn.addEventListener('click', handleSend);
    chatInput.addEventListener('keydown', (e) => { if(e.key === 'Enter') handleSend(); });

    if (window.visualViewport) {
        window.visualViewport.addEventListener('resize', () => {
            if (!document.getElementById('chat-widget').classList.contains('chat-closed')) {
                chatBody.scrollTop = chatBody.scrollHeight;
            }
        });
    }
  </script>
</body>
</html>

