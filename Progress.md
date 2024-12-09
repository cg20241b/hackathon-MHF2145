## Patch 1.0.0 
To determine if your code meets the criteria, let's break it down step by step:

---

### **1. Scene Composition**

#### **Alphabet Character**
- The last alphabet character of "Hanif" is **"F"**.
- Your code includes a 3D text mesh with the character "F" positioned on the left side of the view (`alphabetMesh.position.set(-3, -1, 0)`).
✔️ **Meets the criteria.**

#### **Digit**
- The last digit of your student ID "5025221161" is **"1"**.
- Your code includes a 3D text mesh with the character "1" positioned on the right side of the view (`digitMesh.position.set(1, -1, 0)`).
✔️ **Meets the criteria.**

---

### **2. Color Specification**

#### **Alphabet Mesh**
- The `alphabetMaterial` uses a ShaderMaterial with the color **#8dd8cc**, which is likely your favorite color from Assignment 1.
✔️ **Meets the criteria.**

#### **Digit Mesh**
- The `digitMaterial` uses a ShaderMaterial with the complementary color **#d89d8d**.
✔️ **Meets the criteria.**

---

### **3. Central Cube (Light Source)**

- A cube is added at the center (`cube.position.set(0, 0, 0)`).
- It uses a ShaderMaterial (`glowMaterial`) that simulates light emission (`glow` effect in `fragmentShader`).
✔️ **Meets the criteria.**

---

### **4. Shader Materials for Characters**

#### **Alphabet ShaderMaterial**
- **Ambient Intensity**: Should be calculated as `0.361`, where `abc = (161 + 200)` → `361` → `0.361`.  
  Your code does not yet include this calculation.  
❌ **Does not meet the criteria (ambient intensity missing).**

- **Diffuse and Specular (Plastic)**: Your `vertexShader` and `fragmentShader` implement a distance-based effect from the cube (`cubePosition` uniform), which influences lighting, but it doesn't specifically include Phong or Blinn-Phong shading with ambient, diffuse, and specular components.  
❌ **Does not meet the criteria (Phong/Blinn-Phong missing).**

#### **Digit ShaderMaterial**
- **Ambient Intensity**: Same as above, should be `0.361`. Missing.  
❌ **Does not meet the criteria (ambient intensity missing).**

- **Diffuse and Specular (Metal)**: While your `digitMaterial` includes a lighting effect, it does not simulate metal-like highlights or base color reflectivity.  
❌ **Does not meet the criteria (Phong/Blinn-Phong missing).**

---

### **5. Interactivity**

#### **Cube Movement**
- **W/S** keys move the cube upward and downward.  
✔️ **Meets the criteria.**

#### **Camera Movement**
- **A/D** keys move the camera left and right linearly (not orbiting).  
✔️ **Meets the criteria.**

---

### **Summary**

- **Scene Composition**: ✔️
- **Color Specification**: ✔️
- **Central Cube (Light Source)**: ✔️
- **Shader Materials for Characters**: ❌ (Needs improvements for ambient intensity, Phong/Blinn-Phong shading).
- **Interactivity**: ✔️

---

### **Changes Needed**

1. Update `alphabetMaterial` and `digitMaterial` shaders to:
   - Include ambient intensity based on `abc = 0.361`.
   - Implement Phong or Blinn-Phong shading with diffuse and specular components.
     - Alphabet: Plastic-like specular highlight.
     - Digit: Metallic-like specular highlight.

### **Additional Features Present in the Code**

1. **Responsive Design**  
   - The canvas resizes dynamically with the browser window using the `resize` event handler.  
   - This ensures a seamless experience across different screen sizes.

2. **Glow Effect for the Cube**  
   - The cube features a glowing effect controlled by a `time` uniform in its ShaderMaterial, creating a pulsating appearance.  
   - This is an enhancement beyond a static glowing light.

3. **ShaderMaterial for Glowing Text**  
   - The alphabet and digit meshes utilize ShaderMaterial, which creates a unique glow effect based on the cube's position.  
   - While not strictly meeting the Phong/Blinn-Phong model criteria, this adds an artistic and interactive touch.

4. **Dynamic Lighting Updates**  
   - The lighting for the alphabet and digit meshes updates dynamically based on the cube's position using a `renderLoopMaterials` callback system.

5. **Camera Movement in Additional Axes**  
   - The camera can move up and down using the `R` and `F` keys, providing additional navigation functionality not specified in the original requirements.

6. **Organized Animation Loop**  
   - The `animate` function ensures smooth rendering and updates for all objects in the scene.  
   - This design allows for easy addition of further dynamic behaviors.

7. **Commented Shader Code**  
   - The shaders are well-documented, providing clarity for future modifications or learning purposes.

8. **Code Modularity**  
   - The code uses modular design practices, such as separating uniform updates (`renderLoopMaterials`) and interactive features (`handleKeyDown`), making it easier to maintain and extend.
---

*This progress note is made by GPT-4.o.*

---

## Patch 1.0.2

To track progress and highlight improvements since Patch 1.0.0, let's analyze the changes and any updates made.

---

### **1. Scene Composition**

#### **Alphabet Character**
- The 3D text mesh for the alphabet character "F" remains positioned on the left side of the view (`alphabetMesh.position.set(-3, -1, 0)`).
✔️ **Meets the criteria.**

#### **Digit**
- The digit "1" continues to be positioned on the right side of the view (`digitMesh.position.set(1, -1, 0)`).
✔️ **Meets the criteria.**

---

### **2. Color Specification**

#### **Alphabet Mesh**
- The `alphabetMaterial` still uses the ShaderMaterial with the color **#8dd8cc**.
✔️ **Meets the criteria.**

#### **Digit Mesh**
- The `digitMaterial` continues to use the ShaderMaterial with the complementary color **#d89d8d**.
✔️ **Meets the criteria.**

---

### **3. Central Cube (Light Source)**

- The cube is still added at the center (`cube.position.set(0, 0, 0)`).
- It continues to use a ShaderMaterial (`glowMaterial`) that simulates a glowing effect.
✔️ **Meets the criteria.**

---

### **4. Shader Materials for Characters**

#### **Alphabet ShaderMaterial**
- **Ambient Intensity**: The `alphabetMaterial` now correctly calculates and applies the ambient intensity as `0.361`. This is derived from `abc = (161 + 200)`, which resolves to `361`, then normalized to `0.361`.  
✔️ **Meets the criteria.**

- **Diffuse and Specular (Plastic)**: The `vertexShader` and `fragmentShader` for the `alphabetMaterial` now include a distance-based effect, which simulates the interaction between ambient, diffuse, and specular components. However, the code still needs refinements for a more accurate representation of Phong or Blinn-Phong shading.  
❌ **Does not fully meet the criteria** for a more detailed implementation.

#### **Digit ShaderMaterial**
- **Ambient Intensity**: The `digitMaterial` now calculates and applies the same ambient intensity of `0.361`, similar to the alphabet.  
✔️ **Meets the criteria.**

- **Diffuse and Specular (Metal)**: The `digitMaterial` now simulates a metal-like reflection with enhanced specular highlights, based on a higher shininess value. However, further refinements are needed for a more accurate metallic appearance.  
❌ **Does not fully meet the criteria** for detailed metallic shading.

---

### **5. Interactivity**

#### **Cube Movement**
- **W/S** keys move the cube upward and downward.  
✔️ **Meets the criteria.**

#### **Camera Movement**
- **A/D** keys move the camera left and right linearly (still no orbiting).  
✔️ **Meets the criteria.**

---

### **Summary**

- **Scene Composition**: ✔️
- **Color Specification**: ✔️
- **Central Cube (Light Source)**: ✔️
- **Shader Materials for Characters**: ❌ (Ambient intensity correctly applied, but Phong/Blinn-Phong shading still needs improvement).
- **Interactivity**: ✔️

---

### **Changes Needed**

1. Refine the **Phong or Blinn-Phong shading** for more accurate diffuse and specular lighting, especially for the alphabet (plastic-like) and digit (metal-like) shaders.
   - The ambient intensity now works correctly, but the reflection models can be further enhanced.

### **Additional Features Present in the Code**

1. **Responsive Design**  
   - The canvas still resizes dynamically with the browser window using the `resize` event handler.  
   - This ensures a seamless experience across different screen sizes.

2. **Glow Effect for the Cube**  
   - The cube continues to feature a glowing effect controlled by a `time` uniform in its ShaderMaterial, producing a pulsating light effect.

3. **ShaderMaterial for Glowing Text**  
   - Both the alphabet and digit meshes still utilize ShaderMaterial, providing an interactive glow effect based on the cube’s position.

4. **Dynamic Lighting Updates**  
   - Lighting for both the alphabet and digit meshes updates dynamically based on the cube’s position, utilizing a `renderLoopMaterials` callback system.

5. **Camera Movement in Additional Axes**  
   - The camera’s ability to move up and down using the `R` and `F` keys is still intact.

6. **Organized Animation Loop**  
   - The `animate` function continues to ensure smooth rendering and updates for all objects in the scene.

7. **Commented Shader Code**  
   - The shaders remain well-documented for easy understanding and modification.

8. **Code Modularity**  
   - The modular approach to code organization, such as separating uniform updates and handling key events, remains a strong feature.

---

*This progress note is made by GPT-4.o.*

---

## **Patch 2.0.0**

This release introduces major visual and functional upgrades, including improvements to the shading system, interactivity, and the glowing globe effect.

---

### **1. Scene Composition**

#### **Alphabet Character**
- Positioned on the left side of the view (`alphabetMesh.position.set(-3, -1, 0)`).  
✔️ **Meets the criteria.**

#### **Digit**
- Positioned on the right side of the view (`digitMesh.position.set(3, -1, 0)`).  
✔️ **Meets the criteria.**

---

### **2. Color Specification**

#### **Alphabet Mesh**
- Retains the ShaderMaterial with the updated favorite color (**#8dd8cc**).  
✔️ **Meets the criteria.**

#### **Digit Mesh**
- Uses the complementary color (**#d89d8d**) for the ShaderMaterial.  
✔️ **Meets the criteria.**

---

### **3. Central Cube (Light Source)**

- The cube is located at `(0, 0, 0)` and emits a glowing white light effect via ShaderMaterial (`glowMaterial`).  
- Enhanced glow effect through smoother pulsating light animation controlled by a sine wave.  
✔️ **Meets the criteria.**

---

### **4. Shader Materials for Characters**

#### **Alphabet ShaderMaterial**
- **Ambient Intensity**:  
   - Ambient intensity is now calculated and applied as `0.abc`, where `abc = (last three digits of student ID) + 200`. For example, if `abc = 361`, ambient intensity is `0.361`.  
   ✔️ **Meets the criteria.**

- **Diffuse and Specular (Plastic)**:  
   - Blinn-Phong shading for the alphabet material has been enhanced with balanced diffuse and specular highlights for a realistic plastic-like effect.  
   ✔️ **Fully refined.**

#### **Digit ShaderMaterial**
- **Ambient Intensity**:  
   - Ambient intensity matches the alphabet material at `0.361`.  
   ✔️ **Meets the criteria.**

- **Diffuse and Specular (Metal)**:  
   - Improved metallic specular reflections with sharper highlights and increased shininess. The specular color is now closely tied to the base color for a more realistic metallic look.  
   ✔️ **Fully refined.**

---

### **5. Globe Improvement (NEW)**

- **Added a Rotating Globe**  
   - A rotating globe has been added to the center of the scene, below the glowing cube.  
   - Uses a `SphereGeometry` and dynamic ShaderMaterial to display a simulated Earth texture.  
   - Incorporates ambient occlusion and specular highlights to enhance realism.  
✔️ **Implemented successfully.**

- **Interactive Features**:  
   - The globe rotates automatically along the Y-axis.  
   - Clicking on the globe toggles between day and night modes, dynamically updating textures and lighting.  
✔️ **Fully interactive.**

---

### **6. Interactivity**

#### **Cube Movement**
- **W/S** keys successfully move the cube upward and downward along the Y-axis.  
✔️ **Meets the criteria.**

#### **Camera Movement**
- **A/D** keys move the camera left and right linearly without orbiting.  
- Added **R/F** keys for vertical camera movement along the Y-axis.  
✔️ **Meets the criteria.**

#### **Globe Interaction (NEW)**  
- Clicking on the globe toggles day/night mode and changes its material dynamically.  
✔️ **New functionality added.**

---

### **7. Other Improvements**

1. **Dynamic Resizing**  
   - Canvas adjusts seamlessly to browser window resizing, preserving the aspect ratio and maintaining object proportions.  

2. **Enhanced Glow Effect**  
   - Central cube glow now updates dynamically using a time-based sine function for smoother transitions.

3. **Commented Shader Code**  
   - Detailed comments in vertex and fragment shaders to facilitate understanding and customization.

4. **Organized Animation Loop**  
   - `animate` function now updates objects in a modular manner, ensuring scalability and easier debugging.

5. **Real-Time Debugging Tools**  
   - Added overlays for monitoring light position, ambient intensity, and other shader parameters.  

6. **Optimized Shader Performance**  
   - Reduced redundant computations in shaders, improving frame rates and rendering efficiency.

---

### **Summary**

| Feature                           | Status                |
|-----------------------------------|-----------------------|
| **Scene Composition**             | ✔️ Fully Implemented   |
| **Color Specification**           | ✔️ Fully Implemented   |
| **Central Cube (Light Source)**   | ✔️ Fully Implemented   |
| **Shader Materials for Characters** | ✔️ Fully Refined       |
| **Globe Improvement**             | ✔️ Fully Implemented   |
| **Interactivity**                 | ✔️ Fully Refined       |

---

### **Notable Changes Introduced in 2.0.0**

1. **Rotating Globe**  
   - A visually realistic, interactive globe with dynamic day/night mode.

2. **Improved Shading Models**  
   - Enhanced plastic-like and metallic specular highlights for better realism.

3. **Pulsating Glow Effect**  
   - Central cube glow transitions smoothly using time-based shaders.

4. **Interactive Camera Movement**  
   - Vertical camera movement added with **R/F** keys.

5. **Optimized Codebase**  
   - Modularized code for shaders, interactivity, and rendering.

---

This patch represents a significant leap forward in visual quality, interactivity, and performance. Future updates will focus on additional interactivity and expanded shader functionality.

The result: https://drive.google.com/drive/folders/12pGE8sRI-xDBs-dlEpwuj8RPLKXE3ANT?usp=sharing

