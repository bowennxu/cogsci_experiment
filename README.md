<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Contextual Incongruency Experiment</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: Arial, sans-serif;
            background-color: #f5f5f5;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
        }
        
        .container {
            background: white;
            padding: 40px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            max-width: 900px;
            width: 100%;
        }
        
        h1 {
            color: #333;
            margin-bottom: 20px;
            text-align: center;
        }
        
        .instructions {
            line-height: 1.6;
            color: #555;
            margin-bottom: 30px;
        }
        
        .btn {
            background-color: #4CAF50;
            color: white;
            border: none;
            padding: 15px 30px;
            font-size: 16px;
            border-radius: 5px;
            cursor: pointer;
            display: block;
            margin: 20px auto;
        }
        
        .btn:hover {
            background-color: #45a049;
        }
        
        .btn:disabled {
            background-color: #ccc;
            cursor: not-allowed;
        }
        
        .fixation {
            font-size: 72px;
            text-align: center;
            padding: 100px;
            font-weight: bold;
        }
        
        .trial-counter {
            text-align: center;
            color: #888;
            margin-bottom: 20px;
            font-size: 14px;
        }
        
        .instruction-text {
            text-align: center;
            margin-bottom: 20px;
            font-size: 16px;
            color: #333;
        }
        
        .sentence-container {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 8px;
            margin: 40px 0;
            padding: 20px;
        }
        
        .word-btn {
            background-color: #e0e0e0;
            border: 2px solid #999;
            padding: 10px 15px;
            font-size: 16px;
            border-radius: 5px;
            cursor: pointer;
            transition: all 0.2s;
        }
        
        .word-btn:hover {
            background-color: #d0d0d0;
            transform: translateY(-2px);
        }
        
        .word-btn.correct {
            background-color: #4CAF50;
            color: white;
            border-color: #4CAF50;
        }
        
        .word-btn.incorrect {
            background-color: #f44336;
            color: white;
            border-color: #f44336;
        }
        
        .results {
            text-align: center;
            line-height: 1.8;
        }
        
        .results h2 {
            color: #4CAF50;
            margin-bottom: 20px;
        }
        
        .stats {
            background-color: #f9f9f9;
            padding: 20px;
            border-radius: 5px;
            margin: 20px 0;
            text-align: left;
        }
        
        .participant-id {
            text-align: center;
            color: #888;
            font-size: 12px;
            margin-top: 20px;
        }
        
        .download-btn {
            background-color: #2196F3;
            margin-top: 10px;
        }
        
        .download-btn:hover {
            background-color: #0b7dda;
        }
        
        .hidden {
            display: none;
        }
    </style>
</head>
<body>
    <div class="container">
        <div id="welcome-screen">
            <h1>Contextual Incongruency Experiment</h1>
            <div class="instructions">
                <p><strong>Welcome!</strong> This experiment measures how quickly you can detect contextual incongruencies.</p>
                <br>
                <p><strong>INSTRUCTIONS:</strong></p>
                <ul style="margin-left: 20px; margin-top: 10px;">
                    <li>You will see sentences displayed one at a time</li>
                    <li>Each sentence contains ONE word that doesn't make sense in context</li>
                    <li>Read the sentence and click on the incongruent word as quickly as possible</li>
                    <li>Try to be both FAST and ACCURATE</li>
                </ul>
                <br>
                <p><strong>Example:</strong> "I'm going to drink some hot ice cream"<br>
                (You would click on "ice" or "cream" as they don't fit with "hot drink")</p>
            </div>
            <button class="btn" onclick="startExperiment()">Start Experiment</button>
            <div class="participant-id" id="participant-id-display"></div>
        </div>
        
        <div id="experiment-screen" class="hidden"></div>
    </div>

    <script>
        // Generate participant ID
        function generateParticipantID() {
            const timestamp = Date.now();
            const random = Math.floor(Math.random() * 1000);
            return `P${timestamp}${random}`;
        }

        const participantID = generateParticipantID();
        document.getElementById('participant-id-display').textContent = `Your Participant ID: ${participantID}`;

        // Sentence groups - each participant sees only one group
        const sentenceGroups = {
            group1: [
                // 5 normal capitalization
                {text: "tonight i'm going to paris with my lake in hand", correctIndex: 7, condition: "normal"},
                {text: "she opened the book and started reading the delicious pages", correctIndex: 8, condition: "normal"},
                {text: "he plugged his phone into the angry charger overnight", correctIndex: 6, condition: "normal"},
                {text: "i'm going to wash my hands with fresh concrete before dinner", correctIndex: 8, condition: "normal"},
                {text: "they decided to paint the walls with orange juice this weekend", correctIndex: 7, condition: "normal"},
                // 5 ALL CAPS
                {text: "I NEED TO IRON MY SHIRT WITH THIS FROZEN IRON", correctIndex: 8, condition: "caps"},
                {text: "SHE COMBED HER HAIR USING A FORK FROM THE KITCHEN", correctIndex: 6, condition: "caps"},
                {text: "MY FRIEND WILL BE A FAMOUS FUNGUS ONE DAY", correctIndex: 5, condition: "caps"},
                {text: "WE'RE DRIVING TO WORK IN OUR NEW LIQUID CAR TODAY", correctIndex: 8, condition: "caps"},
                {text: "I'LL CALL YOU LATER ON MY TRANSPARENT TELEPHONE", correctIndex: 7, condition: "caps"}
            ],
            group2: [
                // 5 normal capitalization
                {text: "i need to iron my shirt with this frozen iron", correctIndex: 8, condition: "normal"},
                {text: "she combed her hair using a fork from the kitchen", correctIndex: 6, condition: "normal"},
                {text: "my friend will be a famous fungus one day", correctIndex: 5, condition: "normal"},
                {text: "we're driving to work in our new liquid car today", correctIndex: 8, condition: "normal"},
                {text: "i'll call you later on my transparent telephone", correctIndex: 7, condition: "normal"},
                // 5 ALL CAPS
                {text: "TONIGHT I'M GOING TO PARIS WITH MY LAKE IN HAND", correctIndex: 7, condition: "caps"},
                {text: "SHE OPENED THE BOOK AND STARTED READING THE DELICIOUS PAGES", correctIndex: 8, condition: "caps"},
                {text: "HE PLUGGED HIS PHONE INTO THE ANGRY CHARGER OVERNIGHT", correctIndex: 6, condition: "caps"},
                {text: "I'M GOING TO WASH MY HANDS WITH FRESH CONCRETE BEFORE DINNER", correctIndex: 8, condition: "caps"},
                {text: "THEY DECIDED TO PAINT THE WALLS WITH ORANGE JUICE THIS WEEKEND", correctIndex: 7, condition: "caps"}
            ]
        };

        // Randomly assign participant to a group
        const assignedGroup = Math.random() < 0.5 ? 'group1' : 'group2';
        let sentences = [...sentenceGroups[assignedGroup]];
        
        // Shuffle sentences within the assigned group
        sentences.sort(() => Math.random() - 0.5);

        let currentTrial = 0;
        let startTime = 0;
        let results = [];

        function startExperiment() {
            document.getElementById('welcome-screen').classList.add('hidden');
            document.getElementById('experiment-screen').classList.remove('hidden');
            showFixation();
        }

        function showFixation() {
            const screen = document.getElementById('experiment-screen');
            screen.innerHTML = '<div class="fixation">+</div>';
            
            setTimeout(() => {
                showSentence();
            }, 500);
        }

        function showSentence() {
            if (currentTrial >= sentences.length) {
                showResults();
                return;
            }

            const sentence = sentences[currentTrial];
            const words = sentence.text.split(' ');
            
            const screen = document.getElementById('experiment-screen');
            screen.innerHTML = `
                <div class="trial-counter">Trial ${currentTrial + 1} of ${sentences.length}</div>
                <div class="instruction-text">Click on the word that doesn't fit:</div>
                <div class="sentence-container" id="sentence-container"></div>
            `;

            const container = document.getElementById('sentence-container');
            words.forEach((word, index) => {
                const btn = document.createElement('button');
                btn.className = 'word-btn';
                btn.textContent = word;
                btn.onclick = () => wordClicked(index, sentence.correctIndex, sentence.condition);
                container.appendChild(btn);
            });

            startTime = performance.now();
        }

        function wordClicked(clickedIndex, correctIndex, condition) {
            const reactionTime = performance.now() - startTime;
            const isCorrect = clickedIndex === correctIndex;

            // Visual feedback
            const buttons = document.querySelectorAll('.word-btn');
            buttons.forEach(btn => btn.disabled = true);
            
            if (isCorrect) {
                buttons[clickedIndex].classList.add('correct');
            } else {
                buttons[clickedIndex].classList.add('incorrect');
                buttons[correctIndex].classList.add('correct');
            }

            // Store result
            results.push({
                participantID: participantID,
                assignedGroup: assignedGroup,
                trial: currentTrial + 1,
                condition: condition,
                sentence: sentences[currentTrial].text,
                correctWordIndex: correctIndex,
                clickedWordIndex: clickedIndex,
                reactionTimeMs: Math.round(reactionTime),
                accuracy: isCorrect ? 'correct' : 'incorrect',
                timestamp: new Date().toISOString()
            });

            currentTrial++;
            setTimeout(() => {
                showFixation();
            }, 800);
        }

        function showResults() {
            const correctTrials = results.filter(r => r.accuracy === 'correct');
            const accuracy = (correctTrials.length / results.length) * 100;
            const avgRT = correctTrials.length > 0 
                ? correctTrials.reduce((sum, r) => sum + r.reactionTimeMs, 0) / correctTrials.length 
                : 0;

            const normalTrials = correctTrials.filter(r => r.condition === 'normal');
            const capsTrials = correctTrials.filter(r => r.condition === 'caps');
            const avgRTNormal = normalTrials.length > 0 
                ? normalTrials.reduce((sum, r) => sum + r.reactionTimeMs, 0) / normalTrials.length 
                : 0;
            const avgRTCaps = capsTrials.length > 0 
                ? capsTrials.reduce((sum, r) => sum + r.reactionTimeMs, 0) / capsTrials.length 
                : 0;

            const screen = document.getElementById('experiment-screen');
            screen.innerHTML = `
                <div class="results">
                    <h2>Experiment Complete!</h2>
                    <div class="stats">
                        <p><strong>Your Results:</strong></p>
                        <p>Participant ID: ${participantID}</p>
                        <p>Assigned Group: ${assignedGroup}</p>
                        <p>Total Trials: ${results.length}</p>
                        <p>Accuracy: ${accuracy.toFixed(1)}%</p>
                        <p>Average Reaction Time (correct trials): ${avgRT.toFixed(0)} ms</p>
                        <p>Average RT (normal caps): ${avgRTNormal.toFixed(0)} ms</p>
                        <p>Average RT (ALL CAPS): ${avgRTCaps.toFixed(0)} ms</p>
                    </div>
                    <button class="btn download-btn" onclick="downloadData()">Download Results (CSV)</button>
                    <p style="margin-top: 20px; color: #666;">Thank you for participating!</p>
                </div>
            `;
        }

        function downloadData() {
            // Convert results to CSV
            const headers = ['participantID', 'assignedGroup', 'trial', 'condition', 'sentence', 
                           'correctWordIndex', 'clickedWordIndex', 'reactionTimeMs', 
                           'accuracy', 'timestamp'];
            
            let csv = headers.join(',') + '\n';
            
            results.forEach(result => {
                const row = [
                    result.participantID,
                    result.assignedGroup,
                    result.trial,
                    result.condition,
                    `"${result.sentence}"`,
                    result.correctWordIndex,
                    result.clickedWordIndex,
                    result.reactionTimeMs,
                    result.accuracy,
                    result.timestamp
                ];
                csv += row.join(',') + '\n';
            });

            // Create download link
            const blob = new Blob([csv], { type: 'text/csv' });
            const url = window.URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `${participantID}_results.csv`;
            a.click();
            window.URL.revokeObjectURL(url);
        }
    </script>
</body>
</html>
