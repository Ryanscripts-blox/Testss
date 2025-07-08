<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Painel Roblox</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #1a1a2e, #16213e);
            color: white;
            height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }
        
        .header {
            text-align: center;
            margin-bottom: 30px;
        }
        
        .logo {
            font-size: 48px;
            font-weight: bold;
            background: linear-gradient(45deg, #ff8a00, #e52e71);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            text-shadow: 0 0 10px rgba(229, 46, 113, 0.3);
            margin-bottom: 20px;
        }
        
        .inject-btn {
            background: linear-gradient(45deg, #00c6ff, #0072ff);
            border: none;
            color: white;
            padding: 15px 40px;
            font-size: 18px;
            border-radius: 30px;
            cursor: pointer;
            transition: all 0.3s;
            box-shadow: 0 5px 15px rgba(0, 114, 255, 0.4);
            font-weight: bold;
            margin-top: 10px;
        }
        
        .inject-btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 20px rgba(0, 114, 255, 0.6);
        }
        
        .inject-btn:active {
            transform: translateY(1px);
        }
        
        .panel {
            display: none;
            background: rgba(26, 26, 46, 0.8);
            border-radius: 15px;
            padding: 25px;
            width: 350px;
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
            margin-top: 30px;
            animation: fadeIn 0.5s;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        .panel-title {
            text-align: center;
            margin-bottom: 20px;
            font-size: 24px;
            color: #00c6ff;
        }
        
        .feature {
            display: flex;
            align-items: center;
            margin-bottom: 15px;
            padding: 10px;
            border-radius: 8px;
            transition: all 0.3s;
            cursor: pointer;
        }
        
        .feature:hover {
            background: rgba(255, 255, 255, 0.1);
        }
        
        .feature input {
            margin-right: 15px;
            width: 20px;
            height: 20px;
            cursor: pointer;
        }
        
        .feature label {
            font-size: 16px;
            cursor: pointer;
            flex-grow: 1;
        }
        
        .status {
            color: #00ff88;
            font-size: 14px;
            font-weight: bold;
            margin-left: 10px;
        }
        
        .footer {
            margin-top: 30px;
            text-align: center;
            font-size: 12px;
            color: rgba(255, 255, 255, 0.5);
        }
    </style>
</head>
<body>
    <div class="header">
        <div class="logo">ROBLOX</div>
        <button class="inject-btn" id="injectBtn">INJECT</button>
    </div>
    
    <div class="panel" id="panel">
        <div class="panel-title">PAINEL ROBLOX</div>
        
        <div class="feature">
            <input type="checkbox" id="antiRecording">
            <label for="antiRecording">Anti Gravação</label>
            <span class="status" id="antiRecordingStatus">OFF</span>
        </div>
        
        <div class="feature">
            <input type="checkbox" id="aimbot">
            <label for="aimbot">Aimbot</label>
            <span class="status" id="aimbotStatus">OFF</span>
        </div>
        
        <div class="feature">
            <input type="checkbox" id="aimlock">
            <label for="aimlock">Aimlock</label>
            <span class="status" id="aimlockStatus">OFF</span>
        </div>
        
        <div class="feature">
            <input type="checkbox" id="aimAssist">
            <label for="aimAssist">Aim Assist System</label>
            <span class="status" id="aimAssistStatus">OFF</span>
        </div>
        
        <div class="feature">
            <input type="checkbox" id="umpTrick">
            <label for="umpTrick">UMP Trick</label>
            <span class="status" id="umpTrickStatus">OFF</span>
        </div>
        
        <div class="feature">
            <input type="checkbox" id="recoil">
            <label for="recoil">Diminuir Recuo</label>
            <span class="status" id="recoilStatus">OFF</span>
        </div>
        
        <div class="feature">
            <input type="checkbox" id="accuracy">
            <label for="accuracy">Aumentar Precisão</label>
            <span class="status" id="accuracyStatus">OFF</span>
        </div>
        
        <div class="feature">
            <input type="checkbox" id="scope">
            <label for="scope">Scope</label>
            <span class="status" id="scopeStatus">OFF</span>
        </div>
        
        <div class="footer">
            Painel Roblox © 2023 - Todos os direitos reservados
        </div>
    </div>
    
    <script>
        document.getElementById('injectBtn').addEventListener('click', function() {
            const panel = document.getElementById('panel');
            const injectBtn = document.getElementById('injectBtn');
            
            if (panel.style.display === 'none' || !panel.style.display) {
                panel.style.display = 'block';
                injectBtn.textContent = 'INJECTED';
                injectBtn.style.background = 'linear-gradient(45deg, #00ff88, #00c6ff)';
                
                // Simulação de injeção
                setTimeout(() => {
                    alert('Painel injetado com sucesso! As funções estão agora disponíveis.');
                }, 800);
            } else {
                panel.style.display = 'none';
                injectBtn.textContent = 'INJECT';
                injectBtn.style.background = 'linear-gradient(45deg, #00c6ff, #0072ff)';
            }
        });
        
        // Atualizar status dos checkboxes
        document.querySelectorAll('.feature input').forEach(checkbox => {
            checkbox.addEventListener('change', function() {
                const statusElement = document.getElementById(this.id + 'Status');
                statusElement.textContent = this.checked ? 'ON' : 'OFF';
                statusElement.style.color = this.checked ? '#00ff88' : '#ff4757';
                
                // Simular ativação de função
                if (this.checked) {
                    console.log(`Função ${this.id} ativada (simulação)`);
                } else {
                    console.log(`Função ${this.id} desativada (simulação)`);
                }
            });
        });
    </script>
</body>
</html>
