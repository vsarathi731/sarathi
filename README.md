import React, { useState } from "react";

// Mock data for quests and rewards
const QUESTS = [
  {
    id: 1,
    title: "Start a Conversation",
    description: "Initiate a chat with someone new today.",
    type: "daily",
    psychology: "extrovert",
  },
  {
    id: 2,
    title: "Reflect on a Past Date",
    description: "Record a 1-minute video about what you learned from a previous date.",
    type: "weekly",
    psychology: "introvert",
  },
  {
    id: 3,
    title: "Compliment Someone",
    description: "Send a genuine compliment to a match.",
    type: "daily",
    psychology: "ambivert",
  },
];

const REWARDS = [
  { id: 1, name: "Profile Boost", points: 50 },
  { id: 2, name: "See Who Liked You", points: 100 },
  { id: 3, name: "Super Like", points: 30 },
];

function assignQuests(psychology) {
  // Assign quests based on user psychology
  return QUESTS.filter(q => q.psychology === psychology || q.psychology === "ambivert");
}

function App() {
  const [psychology, setPsychology] = useState("introvert");
  const [quests, setQuests] = useState(assignQuests(psychology));
  const [completed, setCompleted] = useState([]);
  const [points, setPoints] = useState(0);
  const [reflection, setReflection] = useState("");
  const [media, setMedia] = useState(null);
  const [redeemed, setRedeemed] = useState([]);

  const handleComplete = (questId) => {
    if (!reflection && !media) {
      alert("Please add a reflection (text, image, or video)!");
      return;
    }
    setCompleted([...completed, questId]);
    setPoints(points + 20);
    setReflection("");
    setMedia(null);
  };

  const handleRedeem = (reward) => {
    if (points < reward.points) {
      alert("Not enough points!");
      return;
    }
    setPoints(points - reward.points);
    setRedeemed([...redeemed, reward]);
  };

  const handlePsychologyChange = (e) => {
    setPsychology(e.target.value);
    setQuests(assignQuests(e.target.value));
    setCompleted([]);
    setPoints(0);
    setRedeemed([]);
  };

  return (
    <div style={{ maxWidth: 600, margin: "auto", fontFamily: "sans-serif" }}>
      <h1>Self-Growth Quests</h1>
      <label>
        Select your personality type:{" "}
        <select value={psychology} onChange={handlePsychologyChange}>
          <option value="introvert">Introvert</option>
          <option value="extrovert">Extrovert</option>
          <option value="ambivert">Ambivert</option>
        </select>
      </label>
      <h2>Assigned Quests</h2>
      <ul>
        {quests.map(q => (
          <li key={q.id} style={{ marginBottom: 16 }}>
            <strong>{q.title}</strong> ({q.type})<br />
            {q.description}
            <br />
            {completed.includes(q.id) ? (
              <span style={{ color: "green" }}>Completed!</span>
            ) : (
              <div>
                <textarea
                  placeholder="Write your reflection..."
                  value={reflection}
                  onChange={e => setReflection(e.target.value)}
                  rows={2}
                  style={{ width: "100%", marginTop: 8 }}
                />
                <input
                  type="file"
                  accept="image/*,video/*"
                  onChange={e => setMedia(e.target.files[0])}
                  style={{ marginTop: 8 }}
                />
                <button
                  onClick={() => handleComplete(q.id)}
                  style={{ marginTop: 8 }}
                >
                  Mark as Complete
                </button>
              </div>
            )}
          </li>
        ))}
      </ul>
      <h2>Points: {points}</h2>
      <h2>Redeem Rewards</h2>
      <ul>
        {REWARDS.map(r => (
          <li key={r.id}>
            {r.name} ({r.points} pts)
            <button
              onClick={() => handleRedeem(r)}
              disabled={points < r.points || redeemed.includes(r)}
              style={{ marginLeft: 8 }}
            >
              {redeemed.includes(r) ? "Redeemed" : "Redeem"}
            </button>
          </li>
        ))}
      </ul>
      <h2>Redemption History</h2>
      <ul>
        {redeemed.map((r, i) => (
          <li key={i}>{r.name}</li>
        ))}
      </ul>
    </div>
  );
}

export default App;
