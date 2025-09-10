<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculator</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
        }

        .calculator {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 25px;
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.2);
            border: 1px solid rgba(255, 255, 255, 0.2);
            max-width: 350px;
            width: 100%;
        }

        .display {
            background: rgba(0, 0, 0, 0.3);
            border-radius: 15px;
            padding: 25px 20px;
            margin-bottom: 20px;
            text-align: right;
            min-height: 80px;
            display: flex;
            flex-direction: column;
            justify-content: center;
        }

        .display-secondary {
            color: rgba(255, 255, 255, 0.7);
            font-size: 14px;
            min-height: 20px;
            margin-bottom: 5px;
            word-wrap: break-word;
            overflow-wrap: break-word;
        }

        .display-main {
            color: white;
            font-size: 2.5em;
            font-weight: 300;
            word-wrap: break-word;
            overflow-wrap: break-word;
        }

        .buttons {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 12px;
        }

        button {
            padding: 20px;
            font-size: 1.2em;
            border: none;
            border-radius: 12px;
            cursor: pointer;
            transition: all 0.2s ease;
            font-weight: 600;
            background: rgba(255, 255, 255, 0.1);
            color: white;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        button:hover {
            background: rgba(255, 255, 255, 0.2);
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
        }

        button:active {
            transform: translateY(0);
        }

        .btn-clear {
            background: linear-gradient(135deg, #ff6b6b, #ee5a52);
        }

        .btn-clear:hover {
            background: linear-gradient(135deg, #ff5252, #d32f2f);
        }

        .btn-operator {
            background: linear-gradient(135deg, #4ecdc4, #44a08d);
        }

        .btn-operator:hover {
            background: linear-gradient(135deg, #26a69a, #00695c);
        }

        .btn-equals {
            background: linear-gradient(135deg, #45b7d1, #2196f3);
            grid-column: span 2;
        }

        .btn-equals:hover {
            background: linear-gradient(135deg, #1976d2, #0d47a1);
        }

        .btn-zero {
            grid-column: span 2;
        }

        @media (max-width: 400px) {
            .calculator {
                padding: 20px;
                margin: 10px;
            }
            
            button {
                padding: 15px;
                font-size: 1.1em;
            }
            
            .display-main {
                font-size: 2em;
            }
        }
    </style>
</head>
<body>
    <div class="calculator">
        <div class="display">
            <div class="display-secondary" id="secondaryDisplay"></div>
            <div class="display-main" id="mainDisplay">0</div>
        </div>
        <div class="buttons">
            <button class="btn-clear" onclick="clearAll()">AC</button>
            <button class="btn-clear" onclick="clearEntry()">CE</button>
            <button class="btn-operator" onclick="appendOperator('/')">/</button>
            <button class="btn-operator" onclick="appendOperator('*')">×</button>
            
            <button onclick="appendNumber('7')">7</button>
            <button onclick="appendNumber('8')">8</button>
            <button onclick="appendNumber('9')">9</button>
            <button class="btn-operator" onclick="appendOperator('-')">−</button>
            
            <button onclick="appendNumber('4')">4</button>
            <button onclick="appendNumber('5')">5</button>
            <button onclick="appendNumber('6')">6</button>
            <button class="btn-operator" onclick="appendOperator('+')">+</button>
            
            <button onclick="appendNumber('1')">1</button>
            <button onclick="appendNumber('2')">2</button>
            <button onclick="appendNumber('3')">3</button>
            <button onclick="toggleSign()">±</button>
            
            <button class="btn-zero" onclick="appendNumber('0')">0</button>
            <button onclick="appendDecimal()">.</button>
            <button class="btn-equals" onclick="calculate()">=</button>
        </div>
    </div>

    <script>
        let currentInput = '0';
        let previousInput = '';
        let operator = '';
        let waitingForNewNumber = false;

        const mainDisplay = document.getElementById('mainDisplay');
        const secondaryDisplay = document.getElementById('secondaryDisplay');

        function updateDisplay() {
            mainDisplay.textContent = formatNumber(currentInput);
            
            if (previousInput && operator) {
                secondaryDisplay.textContent = `${formatNumber(previousInput)} ${getOperatorSymbol(operator)}`;
            } else {
                secondaryDisplay.textContent = '';
            }
        }

        function formatNumber(num) {
            if (num === '') return '0';
            if (num === '-') return '-';
            
            const number = parseFloat(num);
            if (isNaN(number)) return num;
            
            // Handle very large or very small numbers with scientific notation
            if (Math.abs(number) > 999999999999 || (Math.abs(number) < 0.000001 && number !== 0)) {
                return number.toExponential(6);
            }
            
            // Format with appropriate decimal places
            if (number % 1 === 0) {
                return number.toLocaleString();
            } else {
                return parseFloat(number.toPrecision(12)).toLocaleString();
            }
        }

        function getOperatorSymbol(op) {
            switch(op) {
                case '+': return '+';
                case '-': return '−';
                case '*': return '×';
                case '/': return '/';
                default: return op;
            }
        }

        function appendNumber(num) {
            if (waitingForNewNumber) {
                currentInput = num;
                waitingForNewNumber = false;
            } else {
                if (currentInput === '0') {
                    currentInput = num;
                } else {
                    currentInput += num;
                }
            }
            updateDisplay();
        }

        function appendDecimal() {
            if (waitingForNewNumber) {
                currentInput = '0.';
                waitingForNewNumber = false;
            } else if (currentInput.indexOf('.') === -1) {
                currentInput += '.';
            }
            updateDisplay();
        }

        function appendOperator(op) {
            if (previousInput && operator && !waitingForNewNumber) {
                calculate();
            }
            
            previousInput = currentInput;
            operator = op;
            waitingForNewNumber = true;
            updateDisplay();
        }

        function calculate() {
            if (!previousInput || !operator) return;
            
            const prev = parseFloat(previousInput);
            const current = parseFloat(currentInput);
            
            if (isNaN(prev) || isNaN(current)) return;
            
            let result;
            
            switch(operator) {
                case '+':
                    result = prev + current;
                    break;
                case '-':
                    result = prev - current;
                    break;
                case '*':
                    result = prev * current;
                    break;
                case '/':
                    if (current === 0) {
                        alert('Cannot divide by zero');
                        return;
                    }
                    result = prev / current;
                    break;
                default:
                    return;
            }
            
            currentInput = result.toString();
            previousInput = '';
            operator = '';
            waitingForNewNumber = true;
            updateDisplay();
        }

        function clearAll() {
            currentInput = '0';
            previousInput = '';
            operator = '';
            waitingForNewNumber = false;
            updateDisplay();
        }

        function clearEntry() {
            currentInput = '0';
            waitingForNewNumber = false;
            updateDisplay();
        }

        function toggleSign() {
            if (currentInput !== '0') {
                if (currentInput.startsWith('-')) {
                    currentInput = currentInput.slice(1);
                } else {
                    currentInput = '-' + currentInput;
                }
                updateDisplay();
            }
        }

        // Keyboard support
        document.addEventListener('keydown', function(event) {
            const key = event.key;
            
            if (key >= '0' && key <= '9') {
                appendNumber(key);
            } else if (key === '.') {
                appendDecimal();
            } else if (key === '+') {
                appendOperator('+');
            } else if (key === '-') {
                appendOperator('-');
            } else if (key === '*') {
                appendOperator('*');
            } else if (key === '/') {
                event.preventDefault();
                appendOperator('/');
            } else if (key === 'Enter' || key === '=') {
                calculate();
            } else if (key === 'Escape') {
                clearAll();
            } else if (key === 'Delete') {
                clearEntry();
            }
        });

        // Initialize display
        updateDisplay();
    </script>
</body>
</html>
