html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My LET Reviewer Aid</title>
    <style>
        :root { --p: #0ea5e9; --s: #6366f1; --bg: #0f172a; --card: rgba(30, 41, 59, 0.8); --txt: #f8fafc; --ok: #22c55e; --err: #ef4444; --rat: #f472b6; }
        body { font-family: 'Segoe UI', sans-serif; background: radial-gradient(circle at top left, #1e293b, #0f172a); color: var(--txt); margin: 0; display: flex; justify-content: center; align-items: center; height: 100vh; overflow: hidden; }
        #app { width: 95%; max-width: 1000px; text-align: center; }
        .t-bg { background: rgba(255,255,255,0.1); height: 12px; border-radius: 20px; margin: 15px 0; }
        #t-bar { width: 100%; height: 100%; background: linear-gradient(90deg, var(--p), var(--ok)); border-radius: 20px; transition: width 1s linear; }
        .q-card { background: var(--card); backdrop-filter: blur(12px); padding: 40px; border-radius: 30px; border: 1px solid rgba(255,255,255,0.1); box-shadow: 0 25px 50px rgba(0,0,0,0.5); }
        .tag { display: inline-block; background: linear-gradient(90deg, var(--p), var(--s)); padding: 5px 20px; border-radius: 50px; font-weight: bold; text-transform: uppercase; margin-bottom: 10px; font-size: 1.1rem; }
        .q-txt { font-size: 2.5rem; font-weight: 800; margin-bottom: 30px; line-height: 1.2; }
        .opts { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        .opt { background: rgba(255,255,255,0.05); padding: 20px; border-radius: 15px; cursor: pointer; text-align: left; font-size: 1.6rem; border: 2px solid rgba(255,255,255,0.1); }
        .ok-ans { background: var(--ok) !important; font-weight: bold; border-color: #fff; }
        .err-ans { background: var(--err) !important; border-color: #fff; }
        .r-box { display: none; background: rgba(0,0,0,0.4); padding: 20px; border-radius: 15px; margin-top: 20px; border-left: 6px solid var(--rat); text-align: left; font-size: 1.3rem; line-height: 1.4; overflow-y: auto; max-height: 150px; }
        button { margin-top: 25px; padding: 15px 40px; font-size: 1.6rem; font-weight: bold; border-radius: 50px; border: none; cursor: pointer; background: white; color: #0f172a; transition: 0.3s; }
        .hidden { display: none !important; }
    </style>
</head>
<body>
    <div id="app">
        <div id="quiz-ui">
            <div id="cat" class="tag">Subject</div>
            <div id="t-txt" style="font-size: 1.5rem; font-weight: bold;">30s</div>
            <div class="t-bg"><div id="t-bar"></div></div>
            <div id="count" style="margin-bottom: 10px; opacity: 0.6;">QUESTION 1 OF 100</div>
            <div class="q-card">
                <div id="q-txt" class="q-txt">Loading...</div>
                <div id="opt-c" class="opts"></div>
                <div id="r-box" class="r-box"><strong style="color: var(--rat);">WHY:</strong><br><span id="r-txt"></span></div>
            </div>
            <button id="n-btn" onclick="next()" class="hidden">NEXT QUESTION →</button>
        </div>
        <div id="s-ui" class="hidden">
            <div class="q-card">
                <h1 style="color: var(--p);">Subject Finished!</h1>
                <h2 id="s-name"></h2>
                <div style="font-size: 5rem; font-weight: 900;"><span id="s-val" style="color: var(--ok);">0</span> / 20</div>
                <button onclick="cont()">CONTINUE</button>
            </div>
        </div>
        <div id="f-ui" class="hidden">
            <div class="q-card">
                <h1 style="color: var(--ok);">FUTURE LPT!</h1>
                <p>Mock Exam Complete</p>
                <div style="font-size: 6rem; font-weight: 900;"><span id="f-val" style="color: var(--p);">0</span> / 100</div>
                <button onclick="location.reload()">RESTART</button>
            </div>
        </div>
    </div>

<script>
    let cur=0, tot=0, sub=0, tLeft=30, timer, done=false;
    const db = [
        /* GEN ED (20) */
        { c: "General Education", q: "Smallest unit of life?", a: ["A. Atom","B. Tissue","C. Cell","D. Organ"], ans: 2, r: "The cell is the basic structural/functional unit of life." },
        { c: "General Education", q: "Author of Noli Me Tangere?", a: ["A. Rizal","B. Bonifacio","C. Mabini","D. Luna"], ans: 0, r: "Jose Rizal wrote this to expose Spanish injustices." },
        { c: "General Education", q: "Smallest prime number?", a: ["A. 0","B. 1","C. 2","D. 3"], ans: 2, r: "2 is the only even prime number." },
        { c: "General Education", q: "Red Planet?", a: ["A. Venus","B. Mars","C. Jupiter","D. Saturn"], ans: 1, r: "Mars has iron oxide (rust) on its surface." },
        { c: "General Education", q: "Capital of Philippines?", a: ["A. QC","B. Manila","C. Cebu","D. Davao"], ans: 1, r: "Manila is the seat of government." },
        { c: "General Education", q: "Father of PH Constitution?", a: ["A. Laurel","B. Recto","C. Osmeña","D. Quezon"], ans: 1, r: "Claro M. Recto presided over the 1934 convention." },
        { c: "General Education", q: "Smallest country?", a: ["A. Monaco","B. Vatican","C. Malta","D. Nauru"], ans: 1, r: "Vatican City is the smallest independent state." },
        { c: "General Education", q: "pH of pure water?", a: ["A. 1","B. 5","C. 7","D. 14"], ans: 2, r: "7 is neutral on the pH scale." },
        { c: "General Education", q: "25% of 200?", a: ["A. 25","B. 50","C. 75","D. 100"], ans: 1, r: "200 divided by 4 equals 50." },
        { c: "General Education", q: "Gas plants absorb?", a: ["A. Oxygen","B. Nitrogen","C. CO2","D. Helium"], ans: 2, r: "Plants use CO2 for photosynthesis." },
        { c: "General Education", q: "Largest organ?", a: ["A. Liver","B. Heart","C. Skin","D. Brain"], ans: 2, r: "Skin protects the entire body." },
        { c: "General Education", q: "Binary for 5?", a: ["A. 100","B. 101","C. 110","D. 111"], ans: 1, r: "101 in base 2 is 5." },
        { c: "General Education", q: "Symbol for Gold?", a: ["A. Ag","B. Au","C. Fe","D. Pb"], ans: 1, r: "Au comes from the Latin 'Aurum'." },
        { c: "General Education", q: "First Filipino Saint?", a: ["A. Calungsod","B. Ruiz","C. Rizal","D. Gomez"], ans: 1, r: "Lorenzo Ruiz was canonized in 1987." },
        { c: "General Education", q: "Sum of triangle angles?", a: ["A. 90","B. 180","C. 270","D. 360"], ans: 1, r: "Triangles always sum to 180 degrees." },
        { c: "General Education", q: "Abundant gas in air?", a: ["A. Oxygen","B. Nitrogen","C. CO2","D. Argon"], ans: 1, r: "Nitrogen is 78% of our air." },
        { c: "General Education", q: "Great Plebeian?", a: ["A. Rizal","B. Bonifacio","C. Aguinaldo","D. Mabini"], ans: 1, r: "Andres Bonifacio founded the KKK." },
        { c: "General Education", q: "Smallest state of matter?", a: ["A. Gas","B. Liquid","C. Solid","D. Plasma"], ans: 2, r: "Solids have particles packed closest." },
        { c: "General Education", q: "Chemical for Salt?", a: ["A. H2O","B. NaCl","C. CO2","D. HCl"], ans: 1, r: "Sodium Chloride is common salt." },
        { c: "General Education", q: "Who wrote Iliad?", a: ["A. Homer","B. Plato","C. Aristotle","D. Virgil"], ans: 0, r: "Homer is the Greek epic poet." },

        /* PROF ED (20) */
        { c: "Professional Education", q: "Magna Carta for Teachers?", a: ["A. RA 7836","B. RA 4670","C. RA 9155","D. RA 10533"], ans: 1, r: "RA 4670 protects public teacher welfare." },
        { c: "Professional Education", q: "Multiple Intelligences?", a: ["A. Piaget","B. Gardner","C. Skinner","D. Erikson"], ans: 1, r: "Howard Gardner proposed 8 types." },
        { c: "Professional Education", q: "Blank Slate Theory?", a: ["A. Locke","B. Rousseau","C. Plato","D. Dewey"], ans: 0, r: "John Locke's Tabula Rasa." },
        { c: "Professional Education", q: "Assessment DURING instruction?", a: ["A. Summative","B. Diagnostic","C. Formative","D. Placement"], ans: 2, r: "Formative monitors progress." },
        { c: "Professional Education", q: "Learning by Doing?", a: ["A. Progressivism","B. Perennialism","C. Realism","D. Idealism"], ans: 0, r: "Dewey's Progressivism." },
        { c: "Professional Education", q: "K-12 Law?", a: ["A. RA 9155","B. RA 10533","C. RA 7836","D. RA 4670"], ans: 1, r: "RA 10533 is the K-12 Act." },
        { c: "Professional Education", q: "Father of Behaviorism?", a: ["A. Watson","B. Skinner","C. Pavlov","D. Thorndike"], ans: 0, r: "John B. Watson." },
        { c: "Professional Education", q: "ZPD Proponent?", a: ["A. Piaget","B. Vygotsky","C. Skinner","D. Freud"], ans: 1, r: "Lev Vygotsky." },
        { c: "Professional Education", q: "Highest in Maslow's?", a: ["A. Safety","B. Esteem","C. Self-Act","D. Belonging"], ans: 2, r: "Self-Actualization." },
        { c: "Professional Education", q: "Wait Time promotes?", a: ["A. Silence","B. Thinking","C. Forgetting","D. Guessing"], ans: 1, r: "Provides pause for processing." },
        { c: "Professional Education", q: "Revised Bloom Taxonomy?", a: ["A. Bloom","B. Anderson","C. Piaget","D. Vygotsky"], ans: 1, r: "Lorin Anderson updated the levels." },
        { c: "Professional Education", q: "Pillar: Social Harmony?", a: ["A. Know","B. Do","C. Live Together","D. To Be"], ans: 2, r: "Focuses on empathy and diversity." },
        { c: "Professional Education", q: "Psychosocial Stages?", a: ["A. Piaget","B. Kohlberg","C. Freud","D. Erikson"], ans: 3, r: "Erik Erikson." },
        { c: "Professional Education", q: "Cognitive Development?", a: ["A. Piaget","B. Kohlberg","C. Freud","D. Erikson"], ans: 0, r: "Jean Piaget." },
        { c: "Professional Education", q: "Great Books Philosophy?", a: ["A. Essentialism","B. Perennialism","C. Existentialism","D. Realism"], ans: 1, r: "Perennialism focuses on classic books." },
        { c: "Professional Education", q: "Teacher Stage 4 PPST?", a: ["A. Proficient","B. Highly","C. Distinguished","D. Master"], ans: 2, r: "Distinguished is the highest stage." },
        { c: "Professional Education", q: "Specific to General Method?", a: ["A. Deductive","B. Inductive","C. Direct","D. Inquiry"], ans: 1, r: "Inductive starts with examples." },
        { c: "Professional Education", q: "Hidden Curriculum?", a: ["A. Written","B. Taught","C. Unintended","D. Null"], ans: 2, r: "Lessons from school culture." },
        { c: "Professional Education", q: "Meaningful Learning?", a: ["A. Ausubel","B. Bloom","C. Gagne","D. Skinner"], ans: 0, r: "David Ausubel." },
        { c: "Professional Education", q: "CPD Units required?", a: ["A. 15","B. 30","C. 45","D. 60"], ans: 0, r: "15 units for renewal." },

        /* MATH (20) */
        { c: "Majorship: Mathematics", q: "Slope of 2x + y = 5?", a: ["A. 2","B. -2","C. 5","D. -5"], ans: 1, r: "y = -2x + 5. m = -2." },
        { c: "Majorship: Mathematics", q: "Sum of Hexagon angles?", a: ["A. 360","B. 540","C. 720","D. 900"], ans: 2, r: "(6-2)x180 = 720." },
        { c: "Majorship: Mathematics", q: "Value of sin(30)?", a: ["A. 1/2","B. 1","C. 0","D. √3/2"], ans: 0, r: "sin 30 is 0.5." },
        { c: "Majorship: Mathematics", q: "Square root of 144?", a: ["A. 10","B. 12","C. 14","D. 16"], ans: 1, r: "12x12 = 144." },
        { c: "Majorship: Mathematics", q: "Radius if Diameter 10?", a: ["A. 5","B. 10","C. 20","D. 100"], ans: 0, r: "Radius is half diameter." },
        { c: "Majorship: Mathematics", q: "Value of 5!?", a: ["A. 25","B. 60","C. 120","D. 125"], ans: 2, r: "5x4x3x2x1 = 120." },
        { c: "Majorship: Mathematics", q: "LCM of 12 and 15?", a: ["A. 30","B. 45","C. 60","D. 120"], ans: 2, r: "Smallest multiple is 60." },
        { c: "Majorship: Mathematics", q: "GCF of 24 and 36?", a: ["A. 6","B. 8","C. 12","D. 18"], ans: 2, r: "Highest factor is 12." },
        { c: "Majorship: Mathematics", q: "Derivative of x²?", a: ["A. x","B. 2x","C. 2","D. x³"], ans: 1, r: "2x using power rule." },
        { c: "Majorship: Mathematics", q: "Value of log₂(32)?", a: ["A. 2","B. 4","C. 5","D. 6"], ans: 2, r: "2 to the 5th power is 32." },
        { c: "Majorship: Mathematics", q: "Supplement of 75?", a: ["A. 15","B. 95","C. 105","D. 180"], ans: 2, r: "180 - 75 = 105." },
        { c: "Majorship: Mathematics", q: "Median of {2,5,1,9,7}?", a: ["A. 1","B. 5","C. 7","D. 9"], ans: 1, r: "Middle is 5." },
        { c: "Majorship: Mathematics", q: "Mode of {1,2,2,3}?", a: ["A. 1","B. 2","C. 3","D. 4"], ans: 1, r: "2 appears most." },
        { c: "Majorship: Mathematics", q: "Hypotenuse 6 and 8?", a: ["A. 10","B. 12","C. 14","D. 20"], ans: 0, r: "sqrt(36+64) = 10." },
        { c: "Majorship: Mathematics", q: "ml in 2.5 Liters?", a: ["A. 25","B. 250","C. 2500","D. 25000"], ans: 2, r: "1L = 1000ml." },
        { c: "Majorship: Mathematics", q: "Simplify (x+3)(x-3)?", a: ["A. x²+9","B. x²-9","C. x²+6x+9","D. x²-6x+9"], ans: 1, r: "x² - 9." },
        { c: "Majorship: Mathematics", q: "Value of cos(0)?", a: ["A. 0","B. 1","C. 0.5","D. -1"], ans: 1, r: "cos 0 is 1." },
        { c: "Majorship: Mathematics", q: "Degrees in Hexagon?", a: ["A. 180","B. 360","C. 540","D. 720"], ans: 3, r: "720 total degrees." },
        { c: "Majorship: Mathematics", q: "Area: Base 10, H 8?", a: ["A. 80","B. 40","C. 20","D. 18"], ans: 1, r: "0.5 x 10 x 8 = 40." },
        { c: "Majorship: Mathematics", q: "Distance (0,0) to (3,4)?", a: ["A. 5","B. 7","C. 12","D. 25"], ans: 0, r: "sqrt(3²+4²) = 5." },

        /* ENGLISH (20) */
        { c: "Majorship: English", q: "Father of English Poetry?", a: ["A. Chaucer","B. Milton","C. Spenser","D. Shakespeare"], ans: 0, r: "Geoffrey Chaucer." },
        { c: "Majorship: English", q: "Study of word formation?", a: ["A. Syntax","B. Morphology","C. Phonology","D. Semantics"], ans: 1, r: "Morphology." },
        { c: "Majorship: English", q: "Smallest unit of sound?", a: ["A. Morpheme","B. Phoneme","C. Letter","D. Syllable"], ans: 1, r: "Phoneme." },
        { c: "Majorship: English", q: "Poem of 14 lines?", a: ["A. Ballad","B. Sonnet","C. Epic","D. Haiku"], ans: 1, r: "Sonnet." },
        { c: "Majorship: English", q: "Author: Mockingbird?", a: ["A. Harper Lee","B. Truman","C. Walker","D. Miller"], ans: 0, r: "Harper Lee." },
        { c: "Majorship: English", q: "Denouement means?", a: ["A. Intro","B. Climax","C. Resolution","D. Conflict"], ans: 2, r: "Final resolution." },
        { c: "Majorship: English", q: "Study of sentence structure?", a: ["A. Syntax","B. Morphology","C. Phonology","D. Semantics"], ans: 0, r: "Syntax." },
        { c: "Majorship: English", q: "Author: The Waste Land?", a: ["A. Frost","B. Eliot","C. Yeats","D. Pound"], ans: 1, r: "T.S. Eliot." },
        { c: "Majorship: English", q: "Suffix -ed in 'walked'?", a: ["A. Derivational","B. Inflectional","C. Bound","D. Free"], ans: 1, r: "Inflectional morpheme." },
        { c: "Majorship: English", q: "Word 'Brunch' type?", a: ["A. Clipping","B. Compounding","C. Portmanteau","D. Acronym"], ans: 2, r: "Portmanteau blend." },
        { c: "Majorship: English", q: "Orwell satire on Russia?", a: ["A. 1984","B. Animal Farm","C. Brave New","D. Flies"], ans: 1, r: "Animal Farm." },
        { c: "Majorship: English", q: "Japanese 5-7-5 poem?", a: ["A. Tanka","B. Haiku","C. Noh","D. Kabuki"], ans: 1, r: "Haiku." },
        { c: "Majorship: English", q: "Morning Star of English?", a: ["A. Spenser","B. Chaucer","C. Shakespeare","D. Milton"], ans: 1, r: "Geoffrey Chaucer." },
        { c: "Majorship: English", q: "Study of meaning?", a: ["A. Phonology","B. Morphology","C. Semantics","D. Syntax"], ans: 2, r: "Semantics." },
        { c: "Majorship: English", q: "Author: Dead Stars?", a: ["A. Villa","B. Benitez","C. Joaquin","D. Santos"], ans: 1, r: "Paz Marquez Benitez." },
        { c: "Majorship: English", q: "Soliloquy in a play?", a: ["A. 2 talk","B. Alone talk","C. Narrator","D. Silent"], ans: 1, r: "A character alone on stage." },
        { c: "Majorship: English", q: "Oldest English Epic?", a: ["A. Iliad","B. Odyssey","C. Beowulf","D. Aeneid"], ans: 2, r: "Beowulf." },
        { c: "Majorship: English", q: "Figure: Classroom zoo?", a: ["A. Simile","B. Metaphor","C. Personification","D. Irony"], ans: 1, r: "Direct Metaphor." },
        { c: "Majorship: English", q: "Play: Death Salesman?", a: ["A. Crucible","B. Miller","C. Sons","D. Bridge"], ans: 1, r: "Arthur Miller." },
        { c: "Majorship: English", q: "Author: Jane Eyre?", a: ["A. Emily","B. Charlotte","C. Austen","D. Eliot"], ans: 1, r: "Charlotte Bronte." },

        /* SCIENCE (20) */
        { c: "Majorship: Science", q: "Powerhouse of cell?", a: ["A. Nucleus","B. Ribosome","C. Mitochondria","D. Golgi"], ans: 2, r: "Mitochondria produce ATP." },
        { c: "Majorship: Science", q: "Newton's 1st Law?", a: ["A. Acceleration","B. Interaction","C. Inertia","D. Gravity"], ans: 2, r: "Law of Inertia." },
        { c: "Majorship: Science", q: "Symbol for Water?", a: ["A. H2O","B. CO2","C. O2","D. NaCl"], ans: 0, r: "Two Hydrogen, one Oxygen." },
        { c: "Majorship: Science", q: "pH of neutral solution?", a: ["A. 1","B. 5","C. 7","D. 14"], ans: 2, r: "7 is neutral." },
        { c: "Majorship: Science", q: "Speed of Light?", a: ["A. 3e8 m/s","B. 3e5 m/s","C. 9.8 m/s","D. 343 m/s"], ans: 0, r: "300,000,000 m/s." },
        { c: "Majorship: Science", q: "Hardest substance?", a: ["A. Gold","B. Iron","C. Diamond","D. Quartz"], ans: 2, r: "Diamond is pure carbon." },
        { c: "Majorship: Science", q: "Planet closest to Sun?", a: ["A. Venus","B. Earth","C. Mercury","D. Mars"], ans: 2, r: "Mercury." },
        { c: "Majorship: Science", q: "Study of heredity?", a: ["A. Biology","B. Ecology","C. Genetics","D. Botany"], ans: 2, r: "Genetics." },
        { c: "Majorship: Science", q: "Master Gland?", a: ["A. Thyroid","B. Pituitary","C. Adrenal","D. Pancreas"], ans: 1, r: "Pituitary Gland." },
        { c: "Majorship: Science", q: "Process: Plants food?", a: ["A. Respiration","B. Photosynthesis","C. Digestion","D. Osmosis"], ans: 1, r: "Photosynthesis." },
        { c: "Majorship: Science", q: "Kidney unit?", a: ["A. Neuron","B. Nephron","C. Alveoli","D. Villus"], ans: 1, r: "Nephron filters blood." },
        { c: "Majorship: Science", q: "Every action reaction?", a: ["A. 1st","B. 2nd","C. 3rd","D. 4th"], ans: 2, r: "Newton's 3rd Law." },
        { c: "Majorship: Science", q: "Symbol for Gold?", a: ["A. Ag","B. Au","C. Fe","D. Pb"], ans: 1, r: "Au is Gold." },
        { c: "Majorship: Science", q: "Abundant air gas?", a: ["A. Oxygen","B. Nitrogen","C. CO2","D. Argon"], ans: 1, r: "Nitrogen 78%." },
        { c: "Majorship: Science", q: "Brain coordination?", a: ["A. Cerebrum","B. Cerebellum","C. Stem","D. Thalamus"], ans: 1, r: "Cerebellum." },
        { c: "Majorship: Science", q: "Layer with Ozone?", a: ["A. Tropo","B. Strato","C. Meso","D. Thermo"], ans: 1, r: "Stratosphere." },
        { c: "Majorship: Science", q: "Study of fungi?", a: ["A. Botany","B. Zoology","C. Mycology","D. Phycology"], ans: 2, r: "Mycology." },
        { c: "Majorship: Science", q: "Liquid Earth layer?", a: ["A. Crust","B. Mantle","C. Outer Core","D. Inner"], ans: 2, r: "Outer Core." },
        { c: "Majorship: Science", q: "NaCl is?", a: ["A. Sugar","B. Salt","C. Water","D. Acid"], ans: 1, r: "Sodium Chloride." },
        { c: "Majorship: Science", q: "Gas to solid?", a: ["A. Sublimation","B. Deposition","C. Condense","D. Freeze"], ans: 1, r: "Deposition (frost)." }
    ];

    function startT(){ clearInterval(timer); tLeft=30; updateT(); timer=setInterval(()=>{ tLeft--; updateT(); if(tLeft<=0){ clearInterval(timer); lock(); } }, 1000); }
    function updateT(){ document.getElementById('t-txt').innerText=tLeft+"s"; document.getElementById('t-bar').style.width=(tLeft/30*100)+"%"; }
    function load(){
        done=false; const d=db[cur]; document.getElementById('cat').innerText=d.c; 
        document.getElementById('count').innerText=`QUESTION ${cur+1} OF 100`;
        document.getElementById('q-txt').innerText=d.q; document.getElementById('r-txt').innerText=d.r; 
        document.getElementById('r-box').style.display='none'; document.getElementById('n-btn').classList.add('hidden');
        const c=document.getElementById('opt-c'); c.innerHTML='';
        d.a.forEach((t,i)=>{ const v=document.createElement('div'); v.className='opt'; v.innerText=t; v.onclick=()=>sel(i); c.appendChild(v); }); 
        startT();
    }
    function sel(i){
        if(done) return; done=true; clearInterval(timer); const correct=db[cur].ans; const os=document.getElementsByClassName('opt');
        if(i===correct){ os[i].classList.add('ok-ans'); tot++; sub++; } else { if(i!==-1) os[i].classList.add('err-ans'); os[correct].classList.add('ok-ans'); }
        document.getElementById('r-box').style.display='block'; document.getElementById('n-btn').classList.remove('hidden');
    }
    function lock(){ sel(-1); }
    function next(){ let n=cur+1; if(n>=db.length){ showF(); return; } if(db[n].c!==db[cur].c){ showS(); return; } cur++; load(); }
    function showS(){ document.getElementById('quiz-ui').classList.add('hidden'); document.getElementById('s-ui').classList.remove('hidden'); document.getElementById('s-name').innerText=db[cur].c; document.getElementById('s-val').innerText=sub; }
    function cont(){ sub=0; document.getElementById('s-ui').classList.add('hidden'); document.getElementById('quiz-ui').classList.remove('hidden'); cur++; load(); }
    function showF(){ document.getElementById('quiz-ui').classList.add('hidden'); document.getElementById('f-ui').classList.remove('hidden'); document.getElementById('f-val').innerText=tot; }
    load();
</script>
</body>
</html>
