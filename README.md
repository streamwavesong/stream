// StreamWave App com locução real da ElevenLabs import React, { useState, useEffect } from "react"; import { Card } from "@/components/ui/card"; import { Button } from "@/components/ui/button"; import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs"; import { Input } from "@/components/ui/input"; import { Textarea } from "@/components/ui/textarea";

const ambientTracks = [ { title: "Evening Glow", artist: "Kai Engel", url: "https://freemusicarchive.org/track/evening-glow/download" }, { title: "Celestial Light", artist: "Kai Engel", url: "https://freemusicarchive.org/track/celestial-light/download" }, { title: "Walking Barefoot", artist: "Borrtex", url: "https://freemusicarchive.org/track/walking-barefoot/download" } ];

export default function Home() { const [currentTrack, setCurrentTrack] = useState(0); const [audio, setAudio] = useState(null); const [locucaoTexto, setLocucaoTexto] = useState(""); const [locucaoURLFeminina, setLocucaoURLFeminina] = useState(""); const [locucaoURLMasculina, setLocucaoURLMasculina] = useState("");

useEffect(() => { if (audio) audio.pause(); const newAudio = new Audio(ambientTracks[currentTrack].url); newAudio.play(); newAudio.onended = () => setCurrentTrack((prev) => (prev + 1) % ambientTracks.length); setAudio(newAudio); }, [currentTrack]);

const gerarLocucaoElevenLabs = async () => { if (!locucaoTexto.trim()) return;

const apiKey = "test-API-key"; // Substituir pela sua chave real no ambiente seguro
const VOICES = {
  feminina: "EXAVITQu4vr4xnSDxMaL", // voz padrão ElevenLabs
  masculina: "MF3mGyEYCl7XYWbV9V6O"  // voz masculina padrão
};

const gerarAudio = async (voiceId) => {
  const res = await fetch(https://api.elevenlabs.io/v1/text-to-speech/${voiceId}, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "xi-api-key": apiKey
    },
    body: JSON.stringify({
      text: locucaoTexto,
      model_id: "eleven_monolingual_v1",
      voice_settings: {
        stability: 0.5,
        similarity_boost: 0.5
      }
    })
  });
  const blob = await res.blob();
  return URL.createObjectURL(blob);
};

const urlF = await gerarAudio(VOICES.feminina);
const urlM = await gerarAudio(VOICES.masculina);
setLocucaoURLFeminina(urlF);
setLocucaoURLMasculina(urlM);

};

return ( <div className="p-6 bg-gray-100 min-h-screen"> <h1 className="text-4xl font-bold mb-6">STREAMWAVE</h1>

<Tabs defaultValue="playlists" className="w-full">
    <TabsList className="mb-4">
      <TabsTrigger value="playlists">Rádio Ambiente</TabsTrigger>
      <TabsTrigger value="admin">Painel do Administrador</TabsTrigger>
    </TabsList>

    <TabsContent value="playlists">
      <h2 className="text-2xl font-semibold mb-4">Trilha: Música Ambiente</h2>
      <Card className="p-4 mb-4">
        <p className="text-lg font-medium">{ambientTracks[currentTrack].title}</p>
        <p className="text-sm text-gray-600">{ambientTracks[currentTrack].artist}</p>
      </Card>
      <div className="bg-gray-800 text-white p-4 rounded-xl flex justify-center gap-4">
        <Button onClick={() => audio && audio.pause()}>⏸</Button>
        <Button onClick={() => audio && audio.play()}>►</Button>
        <Button onClick={() => setCurrentTrack((prev) => (prev + 1) % ambientTracks.length)}>►|</Button>
      </div>
    </TabsContent>

    <TabsContent value="admin">
      <h2 className="text-2xl font-semibold mb-4">Painel do Administrador</h2>

      <div className="grid gap-4 mb-6">
        <div>
          <h3 className="text-lg font-medium mb-2">Criar Locução com IA</h3>
          <Textarea
            placeholder="Digite o texto da locução..."
            value={locucaoTexto}
            onChange={(e) => setLocucaoTexto(e.target.value)}
          />
          <Button className="mt-2" onClick={gerarLocucaoElevenLabs}>Gerar Locução</Button>
          {locucaoURLFeminina && (
            <div className="mt-4">
              <p className="text-sm font-semibold">Voz Feminina:</p>
              <audio controls src={locucaoURLFeminina} className="w-full mt-1" />
            </div>
          )}
          {locucaoURLMasculina && (
            <div className="mt-4">
              <p className="text-sm font-semibold">Voz Masculina:</p>
              <audio controls src={locucaoURLMasculina} className="w-full mt-1" />
            </div>
          )}
        </div>

        <div>
          <h3 className="text-lg font-medium mb-2">Agendar Propaganda</h3>
          <Input placeholder="Nome do anúncio" className="mb-2" />
          <Input placeholder="Horário (ex: 12:00)" className="mb-2" />
          <Button>Agendar</Button>
        </div>

        <div>
          <h3 className="text-lg font-medium mb-2">Trilhas Temáticas</h3>
          <ul className="list-disc list-inside">
            <li>Música Ambiente (ativo)</li>
            <li>São João</li>
            <li>Dia do Rock</li>
            <li>Black Friday</li>
          </ul>
          <Button className="mt-2">Editar Trilhas</Button>
        </div>
      </div>
    </TabsContent>
  </Tabs>
</div>

); }
