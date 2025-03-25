前端（React + WebRTC）
javascript
// App.js
import React, { useState, useEffect, useRef } from 'react';
import io from 'socket.io-client';
 
const socket = io('http://localhost:3001');
 
const App = () => {
  const localVideoRef = useRef(null);
  const remoteVideoRef = useRef(null);
  const [localStream, setLocalStream] = useState(null);
  const [remoteStream, setRemoteStream] = useState(null);
 
  useEffect(() => {
    navigator.mediaDevices.getUserMedia({ video: true, audio: true })
      .then(stream => {
        setLocalStream(stream);
        localVideoRef.current.srcObject = stream;
        socket.emit('stream', stream);
      })
      .catch(error => console.error('Error accessing media devices.', error));
 
    socket.on('remoteStream', stream => {
      setRemoteStream(stream);
      remoteVideoRef.current.srcObject = stream;
    });
  }, []);
 
  return (
    <div>
      <h1>Live Streaming App</h1>
      <video ref={localVideoRef} autoPlay muted></video>
      <video ref={remoteVideoRef} autoPlay></video>
    </div>
  );
};
 
export default App;
