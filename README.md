html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>LET Reviewer Aid Pro (30 Questions per Category)</title>
    <style>
        :root {
            --bg-gened: #e3f2fd; --text-gened: #0d47a1;
            --bg-profed: #f1f8e9; --text-profed: #33691e;
            --bg-english: #fff3e0; --text-english: #e65100;
            --bg-math: #f3e5f5; --text-math: #4a148c;
            --bg-science: #e0f2f1; --text-science: #004d40;
        }
        body { margin: 0; font-family: 'Arial Black', sans-serif; height: 100vh; display: flex; flex-direction: column; }
        #menu-screen, #quiz-screen { display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100%; }
        .hidden { display: none !important; }
        h1 { font-size: 5rem; text-align: center; }
        #question-text { font-size: 3rem; text-align: center; padding: 0 80px; margin-bottom: 30px; line-height: 1.2; }
        .options-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; width: 90%; max-width: 1400px; }
        .opt-btn { background: white; border: 5px solid #333; padding: 25px; font-size: 2.2rem; cursor: pointer; border-radius: 15px; font-weight: bold; text-align: left; }
        .correct { background: #2e7d32 !important; color: white; border-color: #1b5e20; }
        .wrong { background: #c62828 !important; color: white; border-color: #8e0000; }
        #ratio-box { margin-top: 25px; width: 85%; padding: 20px; border-radius: 15px; background: white; border: 4px solid #333; font-size: 2rem; text-align: center; visibility: hidden; }
        #hud { position: absolute; top: 20px; width: 95%; display: flex; justify-content: space-between; font-size: 2.5rem; }
        #timer { color: #d32f2f; font-weight: 900; font-size: 4.5rem; }
        .menu-btn { width: 85%; max-width: 800px; padding: 20px; margin: 10px; font-size: 2.5rem; cursor: pointer; border: 5px solid currentColor; background: white; border-radius: 20px; font-weight: bold; }
        .btn { padding: 20px 40px; font-size: 2rem; cursor: pointer; border-radius: 10px; border: none; background: #333; color: white; margin-top: 20px;}
    </style>
</head>
<body id="body">
    <div id="menu-screen">
        <h1>LET REVIEWER AID</h1>
        <button class="menu-btn" style="color: var(--text-gened)" onclick="startQuiz('Gen Ed')">GENERAL EDUCATION</button>
        <button class="menu-btn" style="color: var(--text-profed)" onclick="startQuiz('Prof Ed')">PROFESSIONAL EDUCATION</button>
        <button class="menu-btn" style="color: var(--text-english)" onclick="startQuiz('English')">MAJOR: ENGLISH</button>
        <button class="menu-btn" style="color: var(--text-math)" onclick="startQuiz('Math')">MAJOR: MATH</button>
        <button class="menu-btn" style="color: var(--text-science)" onclick="startQuiz('Science')">MAJOR: SCIENCE</button>
    </div>
    <div id="quiz-screen" class="hidden">
        <div id="hud"><div id="score-display">Score: 0</div><div id="timer">30</div></div>
        <div id="question-text"></div>
        <div class="options-grid" id="options-container">
            <button class="opt-btn" id="optA" onclick="handleChoice('A')"></button>
            <button class="opt-btn" id="optB" onclick="handleChoice('B')"></button>
            <button class="opt-btn" id="optC" onclick="handleChoice('C')"></button>
            <button class="opt-btn" id="optD" onclick="handleChoice('D')"></button>
        </div>
        <div id="ratio-box"></div>
        <button class="btn" id="next-btn" style="display:none; background: #1a237e;" onclick="nextQuestion()">NEXT QUESTION ▶</button>
    </div>

<script>
    const database = {
        "Gen Ed": [
            {q: "Who is the Father of the Philippine Constitution?", a: "A", opts: ["Claro M. Recto", "Jose Laurel", "Cecilia Muñoz-Palma", "Diosdado Macapagal"], r: "Claro M. Recto presided over the 1934 Constitutional Convention."},
            {q: "Which vitamin is synthesized by the skin through sunlight?", a: "C", opts: ["Vitamin A", "Vitamin B12", "Vitamin D", "Vitamin K"], r: "Vitamin D is known as the 'sunshine vitamin' as the skin produces it when exposed to UV rays."},
            {q: "The sum of all angles in a triangle is?", a: "B", opts: ["90°", "180°", "270°", "360°"], r: "Interior angles of any triangle always total 180 degrees."},
            {q: "Who wrote 'Urbana at Feliza'?", a: "A", opts: ["Modesto de Castro", "Francisco Balagtas", "Jose dela Cruz", "Lope K. Santos"], r: "Modesto de Castro is known as the father of Tagalog classic prose for this work."},
            {q: "What is the smallest unit of sound in a language?", a: "A", opts: ["Phoneme", "Morpheme", "Syntax", "Grapheme"], r: "A phoneme is the smallest unit of sound that distinguishes words."},
            {q: "Find the average of integers from 1 to 31.", a: "B", opts: ["15", "16", "31", "32"], r: "Average = (1 + 31) / 2 = 16."},
            {q: "Who was the founder of La Liga Filipina?", a: "B", opts: ["Andres Bonifacio", "Jose Rizal", "Marcelo H. del Pilar", "Apolinario Mabini"], r: "Jose Rizal founded it in 1892 to peacefully unite the archipelago."},
            {q: "Which part of the cell is the powerhouse?", a: "B", opts: ["Nucleus", "Mitochondria", "Ribosome", "Vacuole"], r: "Mitochondria produce ATP, the energy currency of the cell."},
            {q: "What was the first book printed in the Philippines?", a: "B", opts: ["Noli Me Tangere", "Doctrina Christiana", "El Filibusterismo", "Urbana at Feliza"], r: "Doctrina Christiana was printed in 1593."},
            {q: "Who is the 'Brains of the Revolution'?", a: "A", opts: ["Apolinario Mabini", "Emilio Jacinto", "Antonio Luna", "Emilio Aguinaldo"], r: "Mabini was the advisor to Aguinaldo, while Jacinto was the 'Brains of the Katipunan'."},
            {q: "Who is the Father of the Philippine Constitution?", a: "A", opts: ["Claro M. Recto", "Jose Laurel", "Cecilia Muñoz-Palma", "Diosdado Macapagal"], r: "Claro M. Recto presided over the 1934 Constitutional Convention."},
            {q: "Which vitamin is synthesized by the skin when exposed to sunlight?", a: "C", opts: ["Vitamin A", "Vitamin B12", "Vitamin D", "Vitamin K"], r: "Vitamin D is known as the 'sunshine vitamin' produced by UV exposure."},
            {q: "What is the sum of all interior angles in any triangle?", a: "B", opts: ["90°", "180°", "270°", "360°"], r: "Interior angles of any triangle always total 180 degrees."},
            {q: "Who wrote 'Urbana at Feliza'?", a: "A", opts: ["Modesto de Castro", "Francisco Balagtas", "Jose dela Cruz", "Lope K. Santos"], r: "Modesto de Castro is the author of this Tagalog classic."},
            {q: "What is the smallest unit of sound that distinguishes words?", a: "A", opts: ["Phoneme", "Morpheme", "Syntax", "Grapheme"], r: "A phoneme is the smallest unit of sound in speech."},
            {q: "Find the average of integers from 1 to 31.", a: "B", opts: ["15", "16", "31", "32"], r: "Average = (1 + 31) / 2 = 16."},
            {q: "Who was the founder of La Liga Filipina?", a: "B", opts: ["Andres Bonifacio", "Jose Rizal", "Marcelo H. del Pilar", "Apolinario Mabini"], r: "Jose Rizal founded it to peacefully unite the archipelago."},
            {q: "Which part of the cell is the powerhouse?", a: "B", opts: ["Nucleus", "Mitochondria", "Ribosome", "Vacuole"], r: "Mitochondria produce ATP, the energy currency of the cell."},
            {q: "What was the first book printed in the Philippines?", a: "B", opts: ["Noli Me Tangere", "Doctrina Christiana", "El Filibusterismo", "Urbana at Feliza"], r: "Doctrina Christiana was printed in 1593."},
            {q: "Who is the 'Brains of the Revolution'?", a: "A", opts: ["Apolinario Mabini", "Emilio Jacinto", "Antonio Luna", "Emilio Aguinaldo"], r: "Mabini was the advisor to Aguinaldo."},
            {q: "A recipe calls for 2 1/4 cups of flour. If you make 3 batches, how much flour?", a: "D", opts: ["6 1/4", "6 3/4", "7 1/4", "7 3/4"], r: "3 * 2.25 = 6.75 or 6 3/4."},
            {q: "Smallest state of matter?", a: "C", opts: ["Solid", "Liquid", "Gas", "Plasma"], r: "In common states, gas has the least density and definite shape."},
            {q: "Which figure of speech uses 'like' or 'as'?", a: "A", opts: ["Simile", "Metaphor", "Personification", "Hyperbole"], r: "Similes use direct comparison with like/as."},
            {q: "Sunshine Vitamin?", a: "C", opts: ["Vit A", "Vit B", "Vit D", "Vit K"], r: "Vitamin D is produced with UV exposure."},
            {q: "Sum of angles in a square?", a: "D", opts: ["90", "180", "270", "360"], r: "Quadrilaterals total 360 degrees."},
            {q: "Smallest unit of life?", a: "B", opts: ["Atom", "Cell", "Tissue", "Organ"], r: "The cell is the basic functional unit."},
            {q: "Who wrote 'To the Filipino Youth'?", a: "B", opts: ["Bonifacio", "Rizal", "Del Pilar", "Jaena"], r: "Jose Rizal wrote it for the Filipino youth."},
            {q: "Greatest common divisor of 12 and 18?", a: "B", opts: ["3", "6", "9", "12"], r: "6 is the highest factor for both."},
            {q: "Tax on income?", a: "A", opts: ["Income tax", "Excise tax", "Vat", "Estate tax"], r: "Income tax is based on individual earnings."},
            {q: "Planet closest to the sun?", a: "B", opts: ["Venus", "Mercury", "Earth", "Mars"], r: "Mercury is the first planet."},
            {q: "Unit of sound in speech?", a: "A", opts: ["Phoneme", "Morpheme", "Syntax", "Lexeme"], r: "Phonemes distinguish sounds."},
            {q: "Smallest prime number?", a: "A", opts: ["2", "1", "3", "5"], r: "2 is the only even prime."},
            {q: "Study of word meanings?", a: "B", opts: ["Syntax", "Semantics", "Phonetics", "Pragmatics"], r: "Semantics is the study of meaning."},
            {q: "First Tagalog dictionary author?", a: "A", opts: ["Modesto de Castro", "San Buenaventura", "Blancas de San Jose", "Pinpin"], r: "Vocabulario de la Lengua Tagala (1613)."},
            {q: "Process of plants making food?", a: "B", opts: ["Respiration", "Photosynthesis", "Transpiration", "Osmosis"], r: "Photosynthesis uses sunlight."},
            {q: "Who is the Brains of the Katipunan?", a: "B", opts: ["Mabini", "Jacinto", "Bonifacio", "Rizal"], r: "Emilio Jacinto."},
            {q: "Largest ocean?", a: "D", opts: ["Atlantic", "Indian", "Arctic", "Pacific"], r: "Pacific Ocean."},
            {q: "Smallest unit of meaningful sound?", a: "B", opts: ["Phoneme", "Morpheme", "Grammar", "Syntax"], r: "Morphemes carry meaning."},
            {q: "Value of pi approx?", a: "B", opts: ["3.12", "3.14", "3.16", "3.18"], r: "3.14159...."},
            {q: "Unit for measuring sound loudness?", a: "A", opts: ["Decibel", "Hertz", "Newton", "Joule"], r: "Decibels measure intensity."}
        ],

        "Prof Ed": [
            {q: "Who proposed the 'Social Learning Theory'?", a: "D", opts: ["B.F. Skinner", "Jean Piaget", "Lev Vygotsky", "Albert Bandura"], r: "Albert Bandura focused on observational learning."},
            {q: "Who coined the term 'scaffolding'?", a: "A", opts: ["Jerome Bruner", "Jean Piaget", "Lev Vygotsky", "Ivan Pavlov"], r: "While related to Vygotsky's ZPD, the term was coined by Bruner."},
            {q: "Which philosophy emphasizes 'learning by doing'?", a: "C", opts: ["Essentialism", "Existentialism", "Progressivism", "Idealism"], r: "Progressivism, associated with John Dewey, focuses on experiential learning."},
            {q: "A child says the sun is sleeping at night. This is?", a: "A", opts: ["Animism", "Centration", "Egocentrism", "Reversibility"], r: "Animism is attributing human traits to inanimate objects in the pre-operational stage."},
            {q: "Which law is the Magna Carta for Public School Teachers?", a: "B", opts: ["RA 7836", "RA 4670", "RA 9155", "RA 10533"], r: "RA 4670 protects the rights and welfare of public school teachers."},
            {q: "The 'with-it-ness' of a teacher refers to?", a: "B", opts: ["Mastery of content", "Awareness of all classroom events", "Technological skill", "Dressing professionally"], r: "Kounin's with-it-ness is having 'eyes at the back of the head'."},
            {q: "In Freud's theory, the pleasure principle is the?", a: "C", opts: ["Ego", "Superego", "Id", "Alter-ego"], r: "The Id is the primitive part of the personality that seeks instant gratification."},
            {q: "Which is the highest level in Bloom's Revised Taxonomy?", a: "A", opts: ["Creating", "Evaluating", "Analyzing", "Applying"], r: "Creating is the highest cognitive level in the revised taxonomy."},
            {q: "Who proposed the Hierarchy of Needs?", a: "C", opts: ["Sigmund Freud", "Erik Erikson", "Abraham Maslow", "Carl Rogers"], r: "Maslow's hierarchy ranges from physiological needs to self-actualization."},
            {q: "What is the unwritten lessons students learn in school?", a: "C", opts: ["Null curriculum", "Explicit curriculum", "Hidden curriculum", "Taught curriculum"], r: "Hidden curriculum refers to the values and behaviors learned unofficially."},
             {q: "According to John Dewey, education is a process of?", a: "C", opts: ["Rules", "Preparation", "Continuous growth", "Moral values"], r: "Education is a process of living."},
            {q: "The philosophy that reality is based on the mind is?", a: "A", opts: ["Idealism", "Realism", "Pragmatism", "Existentialism"], r: "Idealism views mind as reality."},
            {q: "Who is known as the Father of Modern Education?", a: "C", opts: ["Dewey", "Piaget", "Johann Pestalozzi", "Locke"], r: "Pestalozzi is the father of modern pedagogy."},
            {q: "What is the major conflict during adolescence (Erikson)?", a: "C", opts: ["Trust", "Industry", "Identity vs Role Confusion", "Intimacy"], r: "Identity formation is key in adolescence."},
            {q: "First stage in Piaget's cognitive development theory?", a: "B", opts: ["Pre-operational", "Sensorimotor", "Concrete", "Formal"], r: "Sensorimotor (0-2 years)."},
            {q: "A globe is what type of instructional material?", a: "B", opts: ["Picture", "Model", "Mock up", "Realia"], r: "A globe is a 3D model."},
            {q: "Development stage called the pre-school years?", a: "C", opts: ["Middle Childhood", "Late Infancy", "Early Childhood", "Early Infancy"], r: "Early childhood (2-6)."},
            {q: "Method questioned for reliability due to practice?", a: "C", opts: ["Split half", "Equivalent", "Test-retest", "KR"], r: "Memory affects retakes."},
            {q: "Are vocational teachers required of professional license?", a: "A", opts: ["Yes", "No", "Only if regular", "Less than 5 years"], r: "Teachers must be licensed."},
            {q: "Main concern of perennialism is?", a: "C", opts: ["Skills", "Morals", "Teach eternal truths", "Prepare life"], r: "Focus on classical truths."},
            {q: "Who proposed the 'Social Learning Theory'?", a: "D", opts: ["B.F. Skinner", "Jean Piaget", "Lev Vygotsky", "Albert Bandura"], r: "Bandura focused on observational learning."},
            {q: "What is the unwritten lessons students learn?", a: "C", opts: ["Explicit", "Null", "Hidden", "Taught"], r: "Hidden curriculum includes values/behaviors."},
            {q: "Proponent of 'scaffolding'?", a: "A", opts: ["Jerome Bruner", "Jean Piaget", "Vygotsky", "Pavlov"], r: "Bruner coined scaffolding."},
            {q: "Philosophy: 'Learning by Doing'?", a: "C", opts: ["Essentialism", "Existentialism", "Progressivism", "Idealism"], r: "John Dewey."},
            {q: "Magna Carta for Teachers?", a: "B", opts: ["RA 7836", "RA 4670", "RA 9155", "RA 10533"], r: "RA 4670."},
            {q: "With-it-ness (Kounin)?", a: "B", opts: ["Content mastery", "Awareness", "Dress", "Tech skill"], r: "Awareness of all events."},
            {q: "Freud: Pleasure principle?", a: "C", opts: ["Ego", "Superego", "Id", "Alter-ego"], r: "The Id."},
            {q: "Highest in Bloom Revised?", a: "A", opts: ["Creating", "Evaluating", "Analyzing", "Applying"], r: "Creating."},
            {q: "Hierarchy of Needs?", a: "C", opts: ["Freud", "Erikson", "Maslow", "Rogers"], r: "Abraham Maslow."},
            {q: "ZPD meaning?", a: "A", opts: ["Zone Proximal", "Zero Point", "Zone Potential", "Zero Dev"], r: "Lev Vygotsky."},
            {q: "Proponent of 'Tabula Rasa'?", a: "B", opts: ["Rousseau", "John Locke", "Comenius", "Dewey"], r: "The mind as a blank slate."},
            {q: "Philosophy: 'Back to Basics'?", a: "B", opts: ["Progressivism", "Essentialism", "Idealism", "Existentialism"], r: "Essentialism."},
            {q: "Psychoanalytic personality structure seeking morality?", a: "B", opts: ["Id", "Superego", "Ego", "Libido"], r: "Superego is the moral compass."},
            {q: "K-12 Legal Basis?", a: "B", opts: ["RA 7836", "RA 10533", "RA 9293", "RA 4670"], r: "Enhanced Basic Education Act."},
            {q: "Inductive method is?", a: "A", opts: ["Specific to General", "General to Specific", "Direct", "Review"], r: "Specific observations to general rules."},
            {q: "Summative assessment is?", a: "B", opts: ["During", "After", "Before", "Placement"], r: "Evaluates learning at the end."},
            {q: "Formative assessment is?", a: "A", opts: ["During", "After", "Before", "Placement"], r: "Monitors learning."},
            {q: "Reliability of instrument refers to?", a: "B", opts: ["Truth", "Consistency", "Fairness", "Practicality"], r: "Consistency of results."},
            {q: "Validity refers to?", a: "A", opts: ["Measures target", "Consistency", "Speed", "Difficulty"], r: "Measures what it claims."},
            {q: "Father of Behavioral Psych?", a: "C", opts: ["Pavlov", "Skinner", "Watson", "Thorndike"], r: "John B. Watson."}
        ],
        
        "English": [
            {q: "Who is the first Asian to receive the Nobel Prize for Literature?", a: "B", opts: ["Yasunari Kawabata", "Rabindranath Tagore", "Wole Soyinka", "Gao Xingjian"], r: "Tagore won the Nobel Prize in 1913 for 'Gitanjali'."},
            {q: "What is the smallest unit of meaning in a language?", a: "B", opts: ["Phoneme", "Morpheme", "Syntax", "Lexeme"], r: "A morpheme is the smallest unit that carries meaning."},
            {q: "Who is the foremost Filipino sonneteer?", a: "A", opts: ["Tarrosa Subido", "Jose Garcia Villa", "Angela Manalang Gloria", "Bienvenido Santos"], r: "Tarrosa Subido is widely recognized for her sonnets in English."},
            {q: "Which word has the [zh] sound?", a: "D", opts: ["Ships", "Shore", "She", "Leisure"], r: "The 's' in leisure represents the voiced [zh] sound."},
            {q: "Who wrote 'Alice's Adventures in Wonderland'?", a: "B", opts: ["Rudyard Kipling", "Lewis Carroll", "Hans Christian Andersen", "Charles Dickens"], r: "Lewis Carroll is the pen name of Charles Lutwidge Dodgson."},
            {q: "Who is the Indian counterpart of Shakespeare?", a: "B", opts: ["Tagore", "Kalidasa", "Valmiki", "Vyasa"], r: "Kalidasa wrote dramas like Shakuntala."},
            {q: "English Sonnet: Shakespeare, Italian Sonnet:?", a: "B", opts: ["Alighieri", "Petrarch", "Boccaccio", "Dante"], r: "Francesco Petrarch is the famous Italian sonnet writer."},
            {q: "Who wrote 'A Rose for Emily'?", a: "A", opts: ["Faulkner", "Dickinson", "Woolf", "Shelley"], r: "William Faulkner."},
            {q: "Which word has [z] end sound?", a: "C", opts: ["Maps", "Jokes", "Laughs", "Cars"], r: "The end sound of 'cars' is voiced."},
            {q: "Who wrote 'The Jungle Book'?", a: "B", opts: ["Victor Hugo", "Rudyard Kipling", "William March", "K.L Mansfield"], r: "Rudyard Kipling."},
            {q: "Venus pulchritude means?", a: "B", opts: ["Plain", "Beauty", "Ugliness", "Homeliness"], r: "Pulchritude refers to physical beauty."},
            {q: "Smallest unit of meaning?", a: "B", opts: ["Phoneme", "Morpheme", "Syntax", "Lexeme"], r: "Morphemes carry meaning."},
            {q: "Shakespearean rhyme scheme?", a: "A", opts: ["abab-cdcd-efef-gg", "aabb-ccdd-eeff-gg", "abba-cddc-effe-gg", "abcd-abcd-abcd-ee"], r: "Three quatrains and a couplet."},
            {q: "Literary movement: 'Era of Decadence'?", a: "A", opts: ["Mannerism", "Quality", "Quantity", "Focus"], r: "Aesthetics over morality."},
            {q: "IRI in reading means?", a: "B", opts: ["Intervention", "Inventory", "Innovation", "Integration"], r: "Informal Reading Inventory."},
            {q: "Which phrasal verb means to 'extinguish'?", a: "B", opts: ["Put on", "Put out", "Put off", "Put down"], r: "Put out the fire."},
            {q: "Excessive pride leading to downfall?", a: "B", opts: ["Hamartia", "Hubris", "Catharsis", "Anagnorisis"], r: "Hubris."},
            {q: "Sound system study?", a: "A", opts: ["Phonology", "Morphology", "Syntax", "Semantics"], r: "Phonology."},
            {q: "Minimal pairs develop?", a: "A", opts: ["Listening", "Physical", "Grammar", "Syntax"], r: "Sound discrimination."},
            {q: "Short story 'Urbana at Feliza' author?", a: "A", opts: ["Modesto de Castro", "Balagtas", "Villa", "Rizal"], r: "Father of Tagalog prose."},
            {q: "Robert Frost poems are about?", a: "B", opts: ["Girls", "Decision/Fate", "Faith", "End"], r: "Example: The Road Not Taken."},
            {q: "Sentence pattern: 'She is on a plane'?", a: "B", opts: ["In", "On", "At", "Into"], r: "Preposition use."},
            {q: "Linguistic approach: Internet slang study?", a: "C", opts: ["Historical", "Diachronic", "Synchronic", "Prescriptive"], r: "Study at a point in time."},
            {q: "Error: 'They weren't dangerous criminals they were detectives'?", a: "D", opts: ["Dangling", "Misplaced", "Verbosity", "Run-on"], r: "Run-on sentence."},
            {q: "Essay 'Self Reliance' author?", a: "B", opts: ["Thoreau", "Emerson", "Whiteman", "Stein"], r: "Ralph Waldo Emerson."},
            {q: "Sequence of speaking lesson?", a: "C", opts: ["Practice", "Production", "Presentation", "Review"], r: "PPP Method: Presentation, Practice, Production."},
            {q: "Function of 'that' in 'That Ronnie won surprised Shiela'?", a: "A", opts: ["Noun signal", "Main", "Sub", "Indep"], r: "Noun signal."},
            {q: "Word formation: 'Waterbed'?", a: "B", opts: ["Blending", "Compounding", "Clipping", "Acronym"], r: "Compounding."},
            {q: "Which word has [zh] sound?", a: "D", opts: ["Ships", "Shore", "She", "Leisure"], r: "Voice box vibration."},
            {q: "Literary era: 'Modernism' author?", a: "A", opts: ["Joyce", "Dickens", "Austen", "Wordsworth"], r: "James Joyce."},
            {q: "Father of English Essay?", a: "A", opts: ["Bacon", "Montaigne", "Lamb", "Steele"], r: "Francis Bacon."},
            {q: "Study of sentence structure?", a: "B", opts: ["Morphology", "Syntax", "Phonology", "Semantics"], r: "Syntax."},
            {q: "Voice: 'Active' or 'Passive'?", a: "A", opts: ["Voice", "Mood", "Tense", "Aspect"], r: "Action relation to subject."},
            {q: "Haiku syllables?", a: "B", opts: ["14", "17", "19", "21"], r: "5-7-5 structure."},
            {q: "Wrote 'The Road Not Taken'?", a: "A", opts: ["Frost", "Keats", "Byron", "Shelley"], r: "Robert Frost."}
        ],
       
        "Math": [
            {q: "What is the Least Common Multiple (LCM) of 18 and 24?", a: "D", opts: ["6", "24", "36", "72"], r: "18=2*3^2, 24=2^3*3. LCM=2^3*3^2=72."},
            {q: "If 12 = pq, which proportion is correct?", a: "B", opts: ["6:p=2:q", "4:q=p:3", "p:12=q:1", "12:p=12:q"], r: "Product of extremes = product of means. 4*3 = p*q, so 12=pq."},
            {q: "Find the sum of two numbers if their sum is 48 and difference is 12.", a: "B", opts: ["12 and 40", "30 and 18", "24 and 24", "36 and 12"], r: "x+y=48, x-y=12 -> 2x=60, x=30; y=18."},
            {q: "3.5 kilograms is equal to how many grams?", a: "C", opts: ["35", "350", "3,500", "35,000"], r: "1kg = 1000g, so 3.5kg = 3500g."},
            {q: "What is the value of t^3+5t^2-t+6 if t=-5?", a: "A", opts: ["11", "1", "6", "0"], r: "(-5)^3 + 5(-5)^2 - (-5) + 6 = -125 + 125 + 5 + 6 = 11."},
            {q: "LCM of 18 and 24?", a: "C", opts: ["6", "36", "72", "144"], r: "72 is the smallest multiple."},
            {q: "3 1/2 kilograms in grams?", a: "C", opts: ["35", "350", "3500", "35000"], r: "1kg = 1000g."},
            {q: "30% discount, paid 210. Regular price?", a: "B", opts: ["250", "300", "350", "400"], r: "210 / 0.7 = 300."},
            {q: "Angle hand at 4 o'clock?", a: "B", opts: ["90", "120", "150", "180"], r: "4 * 30 = 120 degrees."},
            {q: "Equation of line (1,-1) parallel to x-2y=6?", a: "C", opts: ["y=x", "x+2y=3", "x-2y=3", "y=2x"], r: "Parallel lines share slope."},
            {q: "Triangle with equal sides?", a: "A", opts: ["Equilateral", "Obtuse", "Right", "Isosceles"], r: "All sides equal."},
            {q: "1/8 in percent?", a: "A", opts: ["12.5%", "8%", "80%", "0.125%"], r: "1 / 8 = 0.125."},
            {q: "Richard 3 days, Alvin 6 days. Together?", a: "A", opts: ["2 days", "3 days", "2.5 days", "4 days"], r: "1/3 + 1/6 = 1/2."},
            {q: "Nika spent 30% snacks, 40% book, had 45 left. Allowance?", a: "C", opts: ["120", "180", "150", "200"], r: "30% left = 45; 45 / 0.3 = 150."},
            {q: "Maria 1000 at 8% compound annually. 2 years?", a: "B", opts: ["1160", "1166.4", "1766", "2166"], r: "1000(1.08)^2 = 1166.4."},
            {q: "Missing term: 5, 20, 80, ___?", a: "A", opts: ["320", "160", "640", "1280"], r: "Multiply by 4."},
            {q: "Factorization of x^2-16?", a: "B", opts: ["(x-4)^2", "(x+4)(x-4)", "(x-8)(x-2)", "x(x-16)"], r: "Difference of squares."},
            {q: "Equality of 2 ratios?", a: "C", opts: ["Quotient", "Fraction", "Proportion", "Rate"], r: "Proportion."},
            {q: "100 mm in cm?", a: "A", opts: ["10", "1", "100", "0.1"], r: "10mm = 1cm."},
            {q: "Area of farm 500m by 40m in hectares?", a: "A", opts: ["2", "20", "200", "0.2"], r: "20,000 sqm = 2 ha."},
            {q: "Equation line x-axis?", a: "B", opts: ["x=0", "y=0", "x=y", "y=1"], r: "The horizontal axis."},
            {q: "Average of two is x+y. If one is x, find other.", a: "C", opts: ["x", "y", "x+2y", "2x+y"], r: "2(x+y) - x = x+2y."},
            {q: "Probability of die divisible by 2?", a: "A", opts: ["1/2", "1/3", "1/4", "1/6"], r: "2, 4, 6 out of 6."},
            {q: "Complementary angle to 30?", a: "A", opts: ["60", "150", "90", "180"], r: "Sum is 90."},
            {q: "Greatest value: 3 + 3^2 + (3+3)^2?", a: "A", opts: ["48", "24", "36", "45"], r: "3 + 9 + 36 = 48."},
            {q: "Aunt half nephew. Formula for total weight?", a: "C", opts: ["3x", "1/2+3x", "(2x)+(x)+(1/2x)", "2x"], r: "Sum of variables."},
            {q: "2 3/4 of 100?", a: "B", opts: ["150", "275", "11/4", "2.75"], r: "11/4 * 100 = 275."},
            {q: "Fraction < 1/3?", a: "B", opts: ["22/13", "15/26", "4/11", "33/98"], r: "Compare values."},
            {q: "Minute hand rotates 1.5 hours in degrees?", a: "E", opts: ["60", "90", "180", "360", "540"], r: "1.5 * 360 = 540."},
            {q: "Roman for 1000?", a: "D", opts: ["L", "C", "D", "M"], r: "M."},
            {q: "Centimeters in 1 meter?", a: "B", opts: ["10", "100", "1000", "0.1"], r: "Metric conversion."},
            {q: "LCM of 4 and 6?", a: "A", opts: ["12", "2", "24", "10"], r: "12 is smallest multiple."},
            
        ],
        "Science": [
            {q: "What is the powerhouse of the cell?", a: "B", opts: ["Nucleus", "Mitochondria", "Ribosome", "Lysosome"], r: "Mitochondria produce the energy (ATP) needed for the cell."},
            {q: "What is the local name of the Philippine Flying Lemur?", a: "A", opts: ["Kagwang", "Manananggal", "Tarsier", "Pawikan"], r: "Kagwang is the local term for this endemic species."},
            {q: "Which planet is known as the Red Planet?", a: "B", opts: ["Venus", "Mars", "Jupiter", "Saturn"], r: "Mars has iron oxide on its surface giving it a red appearance."},
            {q: "Which gas is most abundant in the Earth's atmosphere?", a: "B", opts: ["Oxygen", "Nitrogen", "Carbon Dioxide", "Hydrogen"], r: "Nitrogen makes up about 78% of the atmosphere."},
            {q: "What is the basic functional unit of life?", a: "B", opts: ["Atom", "Cell", "Tissue", "Organ"], r: "The cell is the basic structural and functional unit of all living things."},
            {q: "Which law of motion states that for every action, there is an equal and opposite reaction?", a: "C", opts: ["Law of Inertia", "Law of Acceleration", "Law of Interaction", "Law of Universal Gravitation"], r: "Newton's 3rd Law (Interaction) explains paired forces."},
            {q: "What is the phenomenon where light bends as it passes from one medium to another?", a: "B", opts: ["Reflection", "Refraction", "Diffraction", "Dispersion"], r: "Refraction happens due to the change in light speed between different densities."},
            {q: "In a food chain, which organism occupies the first trophic level?", a: "B", opts: ["Decomposers", "Producers", "Primary Consumers", "Apex Predators"], r: "Producers (plants) convert solar energy into chemical energy."},
            {q: "Which element is the most abundant in the Earth's crust?", a: "A", opts: ["Oxygen", "Silicon", "Aluminum", "Iron"], r: "Oxygen makes up about 46.6% of the Earth's crust by weight."},
            {q: "What type of bond is formed by the sharing of electrons between atoms?", a: "B", opts: ["Ionic Bond", "Covalent Bond", "Metallic Bond", "Hydrogen Bond"], r: "Covalent bonds occur when non-metals share electron pairs."},
            {q: "Which part of the brain controls balance and coordination?", a: "C", opts: ["Cerebrum", "Brainstem", "Cerebellum", "Thalamus"], r: "The cerebellum (little brain) manages motor control and balance."},
            {q: "What is the term for the movement of water across a semi-permeable membrane?", a: "B", opts: ["Diffusion", "Osmosis", "Active Transport", "Capillarity"], r: "Osmosis is a specific type of diffusion involving water."},
            {q: "Which layer of the atmosphere contains the Ozone Layer?", a: "B", opts: ["Troposphere", "Stratosphere", "Mesosphere", "Thermosphere"], r: "The Stratosphere protects Earth from harmful UV radiation via the ozone."},
            {q: "What is the process of a solid changing directly into a gas?", a: "C", opts: ["Evaporation", "Melting", "Sublimation", "Deposition"], r: "Dry ice (CO2) is a common example of sublimation."},
            {q: "In genetics, which law states that alleles separate during gamete formation?", a: "A", opts: ["Law of Segregation", "Law of Independent Assortment", "Law of Dominance", "Law of Linkage"], r: "Mendel's First Law ensures offspring receive one allele from each parent."},
            {q: "What is the unit of electrical resistance?", a: "C", opts: ["Ampere", "Volt", "Ohm", "Watt"], r: "Ohm (Ω) measures the opposition to current flow."},
            {q: "Which blood vessels carry blood away from the heart?", a: "A", opts: ["Arteries", "Veins", "Capillaries", "Venules"], r: "Arteries carry oxygenated blood away (except pulmonary artery)."},
            {q: "What is the most common state of matter in the universe?", a: "D", opts: ["Solid", "Liquid", "Gas", "Plasma"], r: "Stars and nebulae are made primarily of plasma."},
            {q: "Which rock type is formed from the cooling and solidification of magma?", a: "A", opts: ["Igneous", "Sedimentary", "Metamorphic", "Calcite"], r: "Igneous rocks (like granite/basalt) originate from molten rock."},
            {q: "What is the center of an atom consisting of protons and neutrons?", a: "B", opts: ["Electron Cloud", "Nucleus", "Shell", "Valence"], r: "The nucleus contains almost all of the atom's mass."},
            {q: "Which hormone regulates blood sugar levels?", a: "C", opts: ["Adrenaline", "Thyroxine", "Insulin", "Estrogen"], r: "The pancreas secretes insulin to lower blood glucose."},
            {q: "What is the measure of the average kinetic energy of particles in a substance?", a: "B", opts: ["Heat", "Temperature", "Density", "Entropy"], r: "Temperature quantifies how fast particles are moving."},
            {q: "Which planet has the most extensive ring system?", a: "D", opts: ["Jupiter", "Mars", "Neptune", "Saturn"], r: "Saturn's rings are made of ice and rock particles."},
            {q: "What is the study of the interactions between organisms and their environment?", a: "C", opts: ["Biology", "Zoology", "Ecology", "Botany"], r: "Ecology focuses on the ecosystem as a whole."},
            {q: "Which subatomic particle has a negative charge?", a: "C", opts: ["Proton", "Neutron", "Electron", "Positron"], r: "Electrons orbit the nucleus and carry a -1 charge."},
            {q: "What is the result of a neutralized acid and base reaction?", a: "B", opts: ["Gas and Water", "Salt and Water", "Oxygen and Salt", "Acid and Salt"], r: "Neutralization: HCl + NaOH → NaCl + H2O."},
            {q: "Which organ system is responsible for the exchange of gases?", a: "B", opts: ["Circulatory", "Respiratory", "Digestive", "Excretory"], r: "The lungs and airways manage O2 and CO2 exchange."},
            {q: "How many chromosomes are in a normal human somatic cell?", a: "C", opts: ["23", "44", "46", "48"], r: "Humans have 23 pairs, totaling 46 chromosomes."},
            {q: "What is the hardest known natural material?", a: "B", opts: ["Graphite", "Diamond", "Quartz", "Topaz"], r: "Diamond is an allotrope of carbon with a very rigid crystal structure."},
            {q: "Which color of visible light has the shortest wavelength?", a: "D", opts: ["Red", "Green", "Yellow", "Violet"], r: "Violet has the highest frequency and shortest wavelength."},
            {q: "What refers to the ability of an organism to maintain internal stability?", a: "A", opts: ["Homeostasis", "Metabolism", "Anabolism", "Catabolism"], r: "Homeostasis keeps body temp and pH levels steady."},
            {q: "Which planet is known for its 'Great Red Spot'?", a: "C", opts: ["Mars", "Saturn", "Jupiter", "Neptune"], r: "The spot is a giant storm larger than Earth."},
            {q: "What is the main source of energy for cellular processes?", a: "B", opts: ["DNA", "ATP", "RNA", "Glucose"], r: "Adenosine Triphosphate (ATP) is the cell's energy currency."},
            {q: "Which law states that energy cannot be created or destroyed?", a: "A", opts: ["Law of Conservation of Energy", "Law of Thermodynamics", "Lenz's Law", "Ohm's Law"], r: "Energy only changes from one form to another."},
            {q: "What type of mirror curves inward like a cave?", a: "B", opts: ["Convex", "Concave", "Plane", "Diverging"], r: "Concave mirrors can magnify or flip images depending on distance."}
        ]
    };

    // Auto-filler to ensure exactly 30 questions per category if list is incomplete
    for(let cat in database) {
        while(database[cat].length < 30) {
            database[cat].push({
                q: `${cat} Review Item #${database[cat].length + 1}: Analyze the core principles related to this topic area.`,
                a: "A", opts: ["Core Concept", "Distractor 1", "Distractor 2", "Distractor 3"],
                r: "Rationalization: Reviewing past board exam trends helps in identifying critical patterns and logic used by the PRC."
            });
        }
    }

    let currentCat = "", questions = [], currentIndex = 0, score = 0, timerVal = 30, timerInterval, locked = false;

    // Audio Context Setup
    const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    function playSound(freq, type, duration) {
        const osc = audioCtx.createOscillator(); const gain = audioCtx.createGain();
        osc.type = type; osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
        osc.connect(gain); gain.connect(audioCtx.destination);
        gain.gain.setValueAtTime(0.1, audioCtx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + duration);
        osc.start(); osc.stop(audioCtx.currentTime + duration);
    }
    const playCorrect = () => { playSound(523, 'sine', 0.2); setTimeout(()=>playSound(659, 'sine', 0.3), 100); };
    const playWrong = () => { playSound(110, 'sawtooth', 0.5); };

    function startQuiz(cat) {
        currentCat = cat; questions = [...database[cat]].sort(() => Math.random() - 0.5);
        currentIndex = 0; score = 0;
        document.getElementById('menu-screen').classList.add('hidden');
        document.getElementById('quiz-screen').classList.remove('hidden');
        document.getElementById('body').style.background = `var(--bg-${cat.toLowerCase().replace(/\s/g, "")})`;
        loadQuestion();
    }

    function loadQuestion() {
        locked = false; clearInterval(timerInterval); timerVal = 30;
        document.getElementById('timer').innerText = timerVal;
        document.getElementById('ratio-box').style.visibility = "hidden";
        document.getElementById('next-btn').style.display = "none";
        const item = questions[currentIndex];
        document.getElementById('question-text').innerText = item.q;
        ['optA', 'optB', 'optC', 'optD'].forEach((id, i) => {
            const btn = document.getElementById(id);
            btn.innerText = `${String.fromCharCode(65+i)}. ${item.opts[i]}`;
            btn.className = "opt-btn"; btn.disabled = false;
        });
        startTimer();
    }

    function startTimer() {
        timerInterval = setInterval(() => {
            timerVal--; document.getElementById('timer').innerText = timerVal;
            if (timerVal <= 0) { clearInterval(timerInterval); playWrong(); handleChoice(null); }
        }, 1000);
    }

    function handleChoice(userChoice) {
        if(locked) return; locked = true; clearInterval(timerInterval);
        const item = questions[currentIndex]; const correct = item.a;
        const ids = { 'A': 'optA', 'B': 'optB', 'C': 'optC', 'D': 'optD' };
        document.getElementById(ids[correct]).classList.add('correct');
        if(userChoice && userChoice !== correct) {
            playWrong(); document.getElementById(ids[userChoice]).classList.add('wrong');
        } else if (userChoice === correct) {
            playCorrect(); score++; document.getElementById('score-display').innerText = `Score: ${score}`;
        }
        const rBox = document.getElementById('ratio-box');
        rBox.innerHTML = `<strong>RATIONALE:</strong> ${item.r}`;
        rBox.style.visibility = "visible";
        document.getElementById('next-btn').style.display = "block";
        Object.values(ids).forEach(id => document.getElementById(id).disabled = true);
    }

    function nextQuestion() {
        currentIndex++;
        if (currentIndex < 30) loadQuestion();
        else { alert(`Session End! Score: ${score}/30`); location.reload(); }
    }
</script>
</body>
</html>
