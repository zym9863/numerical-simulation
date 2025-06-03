# Dynamic Data Structure Demonstration System

[中文](./README.md) | English

An interactive data structure visualization teaching tool built with Vue 3 + TypeScript + Vite, supporting dynamic demonstration and operation of various common data structures.

## ✨ Project Features

- 🎯 **Interactive Learning** - Real-time manipulation of data structures for intuitive understanding of algorithmic processes
- 🎨 **Modern Interface** - Gradient backgrounds and glassmorphism effects for an elegant user experience
- 📱 **Responsive Design** - Perfect adaptation for both desktop and mobile devices
- ⚡ **High Performance** - Based on Vue 3 Composition API with smooth animation effects
- 🔧 **Modular Architecture** - Component-based design for easy extension and maintenance

## 🚀 Supported Data Structures

### 1. Array
- **Add Element** - Insert new elements at the end of the array
- **Remove Element** - Delete elements by value or index
- **Visual Display** - Clear presentation of array indices and element values

### 2. Linked List
- **Insert Node** - Add new nodes at the end of the linked list
- **Delete Node** - Remove nodes by value
- **Dynamic Display** - Intuitive visualization of connections between nodes

### 3. Stack
- **Push Operation** - Add elements to the top of the stack
- **Pop Operation** - Remove elements from the top of the stack
- **LIFO Demonstration** - Clear visualization of Last In, First Out characteristics

### 4. Queue
- **Enqueue Operation** - Add elements at the rear of the queue
- **Dequeue Operation** - Remove elements from the front of the queue
- **FIFO Demonstration** - Intuitive visualization of First In, First Out characteristics

## 🛠️ Technology Stack

- **Frontend Framework**: Vue 3 (Composition API)
- **Development Language**: TypeScript
- **Build Tool**: Vite
- **Styling**: CSS3 (gradients, animations, responsive design)
- **Development Environment**: Node.js

## 📦 Project Structure

```
src/
├── App.vue                    # Main application component
├── main.ts                    # Application entry file
├── style.css                  # Global stylesheet
├── vite-env.d.ts              # Vite type definitions
├── assets/                    # Static assets directory
└── components/                # Components directory
    ├── ControlPanel.vue       # Control panel component
    └── DataStructureVisualizer.vue  # Data structure visualization component
```

## 🚗 Quick Start

### Requirements

- Node.js >= 16.0.0
- npm or yarn

### Install Dependencies

```bash
npm install
```

### Start Development Server

```bash
npm run dev
```

### Build for Production

```bash
npm run build
```

### Preview Production Build

```bash
npm run preview
```

## 🎮 Usage Instructions

1. **Select Data Structure** - Choose the data structure type to demonstrate in the control panel
2. **Input Data** - Enter initial data or operation parameters as prompted
3. **Execute Operations** - Click corresponding buttons to perform CRUD operations
4. **Observe Animations** - Watch the real-time changes in data structures
5. **Repeat Experiments** - Switch between different data structure types for various experiments

## 🎨 Interface Design

- **Gradient Background** - Purple-blue gradient background for a modern feel
- **Glassmorphism Effect** - Semi-transparent blur effect using backdrop-filter
- **Smooth animations** - Combination of CSS3 animations and Vue transitions
- **Responsive Layout** - Adaptable to various screen sizes
- **Interactive Feedback** - Hover, click and other interactive state feedback

## 🔧 Core Implementation

### Data Structure Operations
- Support for four basic data structures: Array, Linked List, Stack, Queue
- Corresponding add/remove methods for each structure
- Real-time updates to visualization components

### State Management
- Vue 3 reactive state management
- Component communication via emit/props
- Operation step recording for animation display

### Animation System
- CSS3 keyframe animations
- Vue transition components
- Visualization of operation steps

## 🌟 Future Plans

- [ ] Add more data structures (Binary Trees, Graphs, etc.)
- [ ] Include algorithm demonstrations (Sorting, Searching, etc.)
- [ ] Support user-customizable animation speed
- [ ] Add operation history and playback functionality
- [ ] Multi-language support
- [ ] Export animations as video files

## 📄 License

This project is for educational and learning purposes only.

---

**🎓 Educational Value**: This project is particularly suitable for computer science students to learn data structure concepts, providing better understanding of abstract data structure operations through visualization.
