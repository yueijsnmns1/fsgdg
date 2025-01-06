-- Carregando a biblioteca para criar a interface
local Lib = loadstring(game:HttpGet("https://raw.githubusercontent.com/7yhx/kwargs_Ui_Library/main/source.lua"))()

-- Criando a interface de usuário
local UI = Lib:Create{
    Theme = "Dark", -- Tema da interface
    Size = UDim2.new(0, 555, 0, 400) -- Tamanho padrão da UI
}

-- Criando a aba principal
local Main = UI:Tab{
    Name = "Main"
}

-- Adicionando um divisor na aba principal
local Divider = Main:Divider{
    Name = "Funções"
}

-- Tabela para armazenar os ESPs criados para os jogadores
local ESPs = {}

-- Função para criar um ESP para um jogador
local function CreateESP(player)
    local espPart = Instance.new("BillboardGui")
    espPart.Adornee = player.Character:WaitForChild("Head") -- Anexa ao "Head" do jogador
    espPart.Parent = player.Character
    espPart.Size = UDim2.new(0, 100, 0, 100)  -- Tamanho da caixa de ESP
    espPart.StudsOffset = Vector3.new(0, 2, 0) -- Coloca a caixa acima da cabeça

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, 0, 1, 0)
    frame.BackgroundColor3 = Color3.fromRGB(0, 0, 255) -- Cor azul para a caixa (RGB: 0, 0, 255)
    frame.BorderSizePixel = 0
    frame.Parent = espPart

    ESPs[player] = espPart -- Armazenar a referência do ESP para poder removê-lo depois
end

-- Função para remover o ESP de um jogador
local function RemoveESP(player)
    if ESPs[player] then
        ESPs[player]:Destroy()
        ESPs[player] = nil
    end
end

-- Função para atualizar os ESPs quando um jogador entrar
game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        if ESPEnabled then
            CreateESP(player)  -- Cria o ESP se o Toggle estiver ativo
        end
    end)
end)

-- Função para remover ESP quando um jogador sair
game.Players.PlayerRemoving:Connect(function(player)
    RemoveESP(player)
end)

-- Toggle para ativar/desativar o ESP
local ESPEnabled = false  -- Variável que controla o estado do ESP

local ToggleESP = Divider:Toggle{
    Name = "Ativar ESP",
    Description = "Ativa ou desativa o ESP para todos os jogadores.",
    Callback = function(State)
        ESPEnabled = State
        -- Atualiza os ESPs de todos os jogadores atuais no jogo
        for _, player in pairs(game.Players:GetPlayers()) do
            if player.Character then
                if State then
                    CreateESP(player)  -- Cria o ESP se o Toggle estiver ativado
                else
                    RemoveESP(player)  -- Remove o ESP se o Toggle estiver desativado
                end
            end
        end
    end
}

-- Função para matar todos os jogadores (não modificado)
local KillAll = Divider:Button{
    Name = "Matar todos",
    Description = "Mata todos os jogadores no jogo!",
    Callback = function()
        for _, player in pairs(game.Players:GetPlayers()) do
            if player.Character and player.Character:FindFirstChild("Humanoid") then
                player.Character.Humanoid.Health = 0 -- Matar o jogador
            end
        end
        print("Todos os jogadores foram mortos.")
    end
}

-- Função para fechar a interface
local Quit = Main:Button{
    Name = "Fechar Interface",
    Description = "Fecha a interface de usuário.",
    Callback = function()
        UI:Quit{
            Message = "Saindo... Até logo!",
            Length = 1
        }
    end
}
