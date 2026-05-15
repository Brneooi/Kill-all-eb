local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")

-- Busca o evento necessário
local remote = ReplicatedStorage:FindFirstChild("ProjectileExplosion", true)

local Window = Rayfield:CreateWindow({
   Name = "Kill All",
   Icon = 0, 
   LoadingTitle = "Carregando Protocolo de Caos",
   LoadingSubtitle = "by Brenopk",
   Theme = "Default",
   ConfigurationSaving = {
      Enabled = true,
      FolderName = "KillAllCaos", 
      FileName = "Config"
   },
   KeySystem = false,
})

-- Variáveis de controle
local SpamAtivo = false
local MorrerJunto = false
local ModoLag = false
local IntensidadeLag = 20 -- Valor padrão do Slider

local Tab = Window:CreateTab("Principal", 4483362458)

-- 1. CONTROLE MESTRE
local SectionStatus = Tab:CreateSection("Controle Mestre")

local ToggleSpam = Tab:CreateToggle({
   Name = "Ativar Kill All",
   CurrentValue = false,
   Flag = "MainSpam",
   Callback = function(Value)
       SpamAtivo = Value
       
       if SpamAtivo then
           if not remote then
               Rayfield:Notify({Title = "ERRO", Content = "Evento não encontrado!", Duration = 5})
               return
           end

           task.spawn(function()
               while SpamAtivo do
                   for _, p in ipairs(Players:GetPlayers()) do
                       if MorrerJunto or p ~= Players.LocalPlayer then
                           local char = p.Character
                           local root = char and char:FindFirstChild("HumanoidRootPart")
                           
                           if root then
                               -- Se Modo Lag estiver ON, usa a Intensidade do Slider, senão usa 3
                               local repeticoes = ModoLag and IntensidadeLag or 3
                               
                               for i = 1, repeticoes do
                                   remote:FireServer(root, root.Position, nil)
                               end
                           end
                       end
                   end
                   
                   if ModoLag then
                       task.wait() -- Máxima velocidade
                   else
                       task.wait(0.1) -- Estável
                   end
               end
           end)
       else
           Rayfield:Notify({Title = "Status", Content = "Spam Interrompido.", Duration = 2})
       end
   end,
})

-- 2. OPÇÕES DE CONFIGURAÇÃO
local SectionConfig = Tab:CreateSection("Opções")

local ToggleSelf = Tab:CreateToggle({
   Name = "Morrer Junto",
   CurrentValue = false,
   Flag = "SelfKill",
   Callback = function(Value)
       MorrerJunto = Value
   end,
})

local ToggleLag = Tab:CreateToggle({
   Name = "Modo Lag (Ativar Intensidade)",
   CurrentValue = false,
   Flag = "LagMode",
   Callback = function(Value)
       ModoLag = Value
       if Value then
          Rayfield:Notify({Title = "Aviso", Content = "Modo Lag Ativado!", Duration = 2})
       end
   end,
})

-- 3. SLIDER DE INTENSIDADE
local SliderLag = Tab:CreateSlider({
   Name = "Intensidade do Lag (Disparos)",
   Range = {10, 500},
   Increment = 10,
   Suffix = "Projéteis",
   CurrentValue = 20,
   Flag = "LagIntensity",
   Callback = function(Value)
       IntensidadeLag = Value
   end,
})

local SectionInfo = Tab:CreateSection("Status do Evento")
if remote then
    Tab:CreateLabel("Evento detectado: ✅", 4483362458, Color3.fromRGB(0, 255, 100))
else
    Tab:CreateLabel("Evento detectado: ❌", 4483362458, Color3.fromRGB(255, 50, 50))
end
