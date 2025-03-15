
## how to create an array of size n that contain element from 1 to n 
     const a = Array.from({ length: n }, (_, index) => index + 1);

## how to show and hide some thing according to conditions
     
      {Boolean(page) && <button>◀️</button>} 
      // here we have converted the value of page to boolen if its true the it will show the button else it will hide

## Explanation of the Scroll Condition
 
     window.innerHeight + document.documentElement.scrollTop >= 
     document.documentElement.offsetHeight - 10  // Load more 10px before hitting bottom
     window.innerHeight == This gives the height of the visible part of the window
     document.documentElement.scrollTop == This gives the number of pixels scrolled from the top
     document.documentElement.offsetHeight == This gives the total height of the page (including content that is not currently visible)
     Why -10? == The -10 is a small buffer to ensure the request is made just before hitting the bottom, improving responsiveness.

## Throttle Function Implementation

    const throttle = (func, delay) => {
    let lastCall = 0;
    return (...args) => {
    const now = new Date().getTime();
    if (now - lastCall >= delay) {
      lastCall = now;
      func(...args);  }};
    };
    ✅ How it works?

    Limits function execution to once every delay milliseconds.
    Prevents too many function calls while scrolling.






## git log - to check all commits
## git branch - to check all branches present in project
## git branch <branchName> - to create new branch
## git branch --d <branchName> - to delete a branch
## git merge <branchName to be merge in current branch> - to merge a branch
## git checkout <branchName to go inside that branch>- to change the branch
## git add <fileName to be to staged> - to stage single file 
## git add . 
## git commit -m "message" - to commit all changes 
## git log --oneline - it prints all the commits with their commit id 
## git reset <commitId> - it go to given commit id and then removes all the commits after that commit( it remove all the changes made after that commit)
## git revert <commitId> - it switches to just previous commit and add another required commit to that previous commit while keeping connected to latest commit
## git push-- to push the repo     

# 
# 
# to upload a folder of prebuilt project from local to github on master branch 
### cd path/to/your/project
### git init
### git remote add origin https://github.com/yourusername/your-repository-name.git
### git add .
### git commit -m "Initial commit"
### git push 




#
# 
# How to update an existing repo on github
### go to repo
### git add .
### git commit -m "commit message here" Enter
### git push origin main Enter





### import { useEffect, useRef } from 'react';
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';
import startIconImage from '../assets/startIconImage.png'; // Your starting point image
import endIconImage from '../assets/endIconImage.png'; // Your ending point image
import vessel from '../assets/vessel.png'; // Vessel image

const MapComponent = () => {
  const mapRef = useRef(null);

  useEffect(() => {
    // Initialize map
    const map = L.map(mapRef.current,{attributionControl:false}).setView([22.1696, 91.4996], 30);

    // Add OpenStreetMap tile layer
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      maxZoom: 10,
    }).addTo(map);

    // Starting and ending coordinates
    const startCoordinates = [22.1696, 91.4996];
    const endCoordinates = [22.2637, 91.7159];

    // Custom icon for the starting point
    const startIcon = L.icon({
      iconUrl: startIconImage,
      iconSize: [25, 25], // Adjust the size of the icon as needed
      iconAnchor: [12, 25], // Anchor the icon appropriately (optional)
    });

    // Custom icon for the ending point
    const endIcon = L.icon({
      iconUrl: endIconImage,
      iconSize: [25, 25], // Adjust the size of the icon as needed
      iconAnchor: [12, 25], // Anchor the icon appropriately (optional)
    });

    // Place markers with custom icons for start and end points
    L.marker(startCoordinates, { icon: startIcon }).addTo(map); // Starting point marker
    L.marker(endCoordinates, { icon: endIcon }).addTo(map); // Ending point marker

    // Calculate the bearing (angle) between the start and end coordinates
    const bearing = calculateBearing(startCoordinates, endCoordinates);

    // Custom vessel icon with rotation
    const vesselIcon = L.divIcon({
      html: `<img src="${vessel}" style="transform: rotate(${bearing}deg); width: 10px; height: 50px;">`,
      className: 'vessel-icon',
    });
    const start=[22.226, 91.530] //// customized points of vessel
    // Place the vessel marker at the start coordinates
    const marker = L.marker(start, { icon: vesselIcon }).addTo(map);

    // Define the speed and calculate total time for the vessel's movement
    const speed = 20; // speed in km/h
    const distance = haversineDistance(startCoordinates, endCoordinates);
    const totalTime = (distance / speed) ; // in seconds

    let progress = 0;
    const intervalTime = 0.5; // 2FPS -> 0.5 seconds

    const interval = setInterval(() => {
      progress += intervalTime / totalTime;



      const end=[22.2920, 91.690];
      // Stop at the end
      if (progress >= 1) {
        clearInterval(interval);
        marker.setLatLng(end); // Set final position
        return;
      }

      // Linear interpolation of coordinates
      const newLat =
        start[0] +
        (end[0] - start[0]) * progress;
      const newLng =
        start[1] +
        (end[1] - start[1]) * progress;

      // Move the vessel marker to the new interpolated position
      marker.setLatLng([newLat, newLng]);

      // Optionally recenter the map
      map.setView([newLat, newLng]);
    }, intervalTime * 1000);

    // Clean up interval and map on unmount
    return () => {
      clearInterval(interval);
      map.remove();
    };
  }, []);

  // Haversine formula to calculate distance between two coordinates
  const haversineDistance = (coords1, coords2) => {
    const R = 6371; // Radius of the Earth in km
    const lat1 = coords1[0];
    const lon1 = coords1[1];
    const lat2 = coords2[0];
    const lon2 = coords2[1];

    const dLat = ((lat2 - lat1) * Math.PI) / 180;
    const dLon = ((lon2 - lon1) * Math.PI) / 180;

    const a =
      Math.sin(dLat / 2) * Math.sin(dLat / 2) +
      Math.cos((lat1 * Math.PI) / 180) *
        Math.cos((lat2 * Math.PI) / 180) *
        Math.sin(dLon / 2) * Math.sin(dLon / 2);

    const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));

    return R * c; // Distance in km
  };

  // Function to calculate the bearing between two points
  const calculateBearing = (start, end) => {
    const [lat1, lon1] = start;
    const [lat2, lon2] = end;

    const dLon = ((lon2 - lon1) * Math.PI) / 180;
    const lat1Rad = (lat1 * Math.PI) / 180;
    const lat2Rad = (lat2 * Math.PI) / 180;

    const y = Math.sin(dLon) * Math.cos(lat2Rad);
    const x =
      Math.cos(lat1Rad) * Math.sin(lat2Rad) -
      Math.sin(lat1Rad) * Math.cos(lat2Rad) * Math.cos(dLon);

    const bearingRad = Math.atan2(y, x);
    const bearingDeg = (bearingRad * 180) / Math.PI;

    return (bearingDeg + 360) % 360; // Normalize to 0-360 degrees
  };

  return (
    <div>
      <div className="info-panel">
        <h3>Starting</h3>
        <p>Lat: 22.1696 | Long: 91.4996</p>
        <h3>Ending</h3>
        <p>Lat: 22.2637 | Long: 91.7159</p>
        <h3>Speed: 20 km/h</h3>
      </div>
      <div
        id="map"
        ref={mapRef}
        style={{ height: '500px', width: '100%' }}
      ></div>
    </div>
  );
};

export default MapComponent;

