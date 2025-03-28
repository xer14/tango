import React, { useState } from "react";
import { BrowserRouter as Router, Routes, Route, Link } from "react-router-dom";
import { motion } from "framer-motion";

// Responsive Styles
const styles = `
  body { font-family: Arial, sans-serif; background: #f4f4f4; margin: 0; padding: 0; }
  nav { background: #ff6347; padding: 1rem; display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; }
  .logo-container { display: flex; align-items: center; }
  .logo-container img { height: 50px; margin-right: 10px; cursor: pointer; }
  nav a { color: white; text-decoration: none; font-weight: bold; padding: 0.5rem; }
  .page { padding: 2rem; text-align: center; max-width: 900px; margin: auto; }
  .editable { border: 1px solid #ccc; padding: 10px; min-height: 50px; cursor: text; }
  .media-container { display: flex; flex-wrap: wrap; gap: 10px; justify-content: center; }
  .media-container img, .media-container video { width: 120px; height: 120px; object-fit: cover; border-radius: 10px; cursor: pointer; }
  .iframe-container { display: flex; justify-content: center; margin-top: 10px; }
  iframe { width: 100%; max-width: 800px; height: 450px; border: none; }
  input[type="file"] { display: block; margin: 10px auto; }
  .fullscreen-media { position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; background: rgba(0, 0, 0, 0.9); display: flex; align-items: center; justify-content: center; z-index: 1000; }
  .fullscreen-media img, .fullscreen-media video { max-width: 90%; max-height: 90%; }
  .close-btn { position: absolute; top: 20px; right: 20px; background: white; border: none; padding: 10px; cursor: pointer; font-size: 16px; }
  @media (max-width: 768px) {
    .page { padding: 1rem; }
    nav { flex-direction: column; align-items: center; }
    nav a { padding: 0.5rem 0; }
    iframe { height: 300px; }
  }
`;

const Home = ({ images, onMediaUpload }) => (
  <motion.div className="page" initial={{ opacity: 0 }} animate={{ opacity: 1 }}>
    <h1>Welcome to LRY REPAIR AND PRINTING SERVICES</h1>
    <p>Your trusted partner for repair and printing services.</p>
    <input type="file" multiple accept="image/*" onChange={onMediaUpload} />
    <div className="media-container">
      {images.map((file, index) => (
        <img key={index} src={file.url} alt="Uploaded" />
      ))}
    </div>
  </motion.div>
);

const OurWork = ({ videos, onMediaClick }) => (
  <motion.div className="page" initial={{ opacity: 0 }} animate={{ opacity: 1 }}>
    <h1>Our Work</h1>
    <p>See some of our recent projects and satisfied clients.</p>
    <div className="media-container">
      {videos.map((file, index) => (
        <video key={index} controls onClick={() => onMediaClick(file)}>
          <source src={file.url} type={file.type} />
        </video>
      ))}
    </div>
  </motion.div>
);

const App = () => {
  const [logo, setLogo] = useState(null);
  const [images, setImages] = useState([]);
  const [videos, setVideos] = useState([]);
  const [fullscreenMedia, setFullscreenMedia] = useState(null);

  const handleLogoUpload = (e) => {
    const file = e.target.files[0];
    if (file) {
      const logoURL = URL.createObjectURL(file);
      setLogo(logoURL);
    }
  };

  const handleMediaUpload = (e) => {
    const files = Array.from(e.target.files);
    const imageFiles = files.filter(file => file.type.startsWith("image")).map(file => ({
      url: URL.createObjectURL(file),
      type: file.type
    }));
    const videoFiles = files.filter(file => file.type.startsWith("video")).map(file => ({
      url: URL.createObjectURL(file),
      type: file.type
    }));
    setImages(prevImages => [...prevImages, ...imageFiles]);
    setVideos(prevVideos => [...prevVideos, ...videoFiles]);
  };

  return (
    <>
      <style>{styles}</style>
      <Router>
        <nav>
          <div className="logo-container">
            {logo ? <img src={logo} alt="Logo" /> : <span style={{ color: 'white', fontWeight: 'bold' }}>Upload Logo</span>}
            <input type="file" accept="image/*" onChange={handleLogoUpload} />
          </div>
          <div>
            <Link to="/">Home</Link>
            <Link to="/our-work">Our Work</Link>
          </div>
        </nav>
        <Routes>
          <Route path="/" element={<Home images={images} onMediaUpload={handleMediaUpload} />} />
          <Route path="/our-work" element={<OurWork videos={videos} onMediaClick={setFullscreenMedia} />} />
        </Routes>
        {fullscreenMedia && (
          <div className="fullscreen-media" onClick={() => setFullscreenMedia(null)}>
            {fullscreenMedia.type.startsWith("image") ? (
              <img src={fullscreenMedia.url} alt="Fullscreen" />
            ) : (
              <video controls autoPlay>
                <source src={fullscreenMedia.url} type={fullscreenMedia.type} />
              </video>
            )}
            <button className="close-btn" onClick={() => setFullscreenMedia(null)}>Close</button>
          </div>
        )}
      </Router>
    </>
  );
};

export default App;
