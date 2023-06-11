# To-do-list

## AIM:
To create a fully functional to do list.
## PROGRAM:
```
Index.html:
<!DOCTYPE html>
<html lang=”en”>
<head>
 <meta charset=”utf-8” />
 <link rel=”icon” href=”%PUBLIC_URL%/favicon.ico” />
 <meta name=”viewport” content=”width=device-width, initial-scale=1” />
 <meta name=”theme-color” content=”#000000” />
 <meta name=”description” content=”Web site created using create-react-app” />
 <link rel=”apple-touch-icon” href=”%PUBLIC_URL%/logo192.png” />
 <!—
 Manifest.json provides metadata used when your web app is installed on a
 User’s mobile device or desktop. See https://developers.google.com/web/fundamentals/web-appmanifest/
 →
 <link rel=”manifest” href=”%PUBLIC_URL%/manifest.json” />
 <!—
 Notice the use of %PUBLIC_URL% in the tags above.
 It will be replaced with the URL of the `public` folder during the build.
 Only files inside the `public` folder can be referenced from the HTML.
 Unlike “/favicon.ico” or “favicon.ico”, “%PUBLIC_URL%/favicon.ico” will
 Work correctly both with client-side routing and a non-root public URL.
 Learn how to configure a non-root public URL by running `npm run build`.
 →
 <title>Todo App</title>
</head>
<body>
 <noscript>You need to enable JavaScript to run this app.</noscript>
 <div id=”root”></div>
 <!—
 This HTML file is a template.
 If you open it directly in the browser, you will see an empty page.
 You can add webfonts, meta tags, or analytics to this file.
 The build step will place the bundled scripts into the <body> tag.
 To begin the development, run `npm start` or `yarn start`.
 To create a production bundle, use `npm run build` or `yarn build`.
 →
</body>
</html>
Store.js:
Import { configureStore } from ‘@reduxjs/toolkit’;
Import todoReducer from ‘../slices/todoSlice’;
Export const store = configureStore({
 Reducer: {
 Todo: todoReducer,
 },
});
Appcontent.js:
Import { AnimatePresence, motion } from ‘framer-motion’;
Import React from ‘react’;
Import { useSelector } from ‘react-redux’;
Import styles from ‘../styles/modules/app.module.scss’;
Import TodoItem from ‘./TodoItem’;
Const container = {
 Hidden: { opacity: 1 },
 Visible: {
 Opacity: 1,
 Scale: 1,
 Transition: {
 staggerChildren: 0.2,
 },
 },
};
Const child = {
 Hidden: { y: 20, opacity: 0 },
 Visible: {
 Y: 0,
 Opacity: 1,
 },
};
Function AppContent() {
 Const todoList = useSelector((state) => state.todo.todoList);
 Const filterStatus = useSelector((state) => state.todo.filterStatus);
 Const sortedTodoList = […todoList];
 sortedTodoList.sort((a, b) => new Date(b.time) – new Date(a.time));
 const filteredTodoList = sortedTodoList.filter((item) => {
 if (filterStatus === ‘all’) {
 return true;
 }
 Return item.status === filterStatus;
 });
 Return (
 <motion.div
 className={styles.content__wrapper}
 variants={container}
 initial=”hidden”
 animate=”visible”
 >
 <AnimatePresence>
 {filteredTodoList && filteredTodoList.length > 0 ? (
 filteredTodoList.map((todo) => (
 // <motion.div key={todo.id} variants={child}>
 <TodoItem key={todo.id} todo={todo} />
 // </motion.div>
 ))
 ) : (
 <motion.p variants={child} className={styles.emptyText}>
 No Todos
 </motion.p>
 )}
 </AnimatePresence>
 </motion.div>
 );
}
Export default AppContent;
Appheader.js:
Import React, { useState } from ‘react’;
Import { useDispatch, useSelector } from ‘react-redux’;
Import Button, { SelectButton } from ‘./Button’;
Import styles from ‘../styles/modules/app.module.scss’;
Import TodoModal from ‘./TodoModal’;
Import { updateFilterStatus } from ‘../slices/todoSlice’;
Function AppHeader() {
 Const [modalOpen, setModalOpen] = useState(false);
 Const initialFilterStatus = useSelector((state) => state.todo.filterStatus);
 Const [filterStatus, setFilterStatus] = useState(initialFilterStatus);
 Const dispatch = useDispatch();
 Const updateFilter = € => {
 setFilterStatus(e.target.value);
 dispatch(updateFilterStatus(e.target.value));
 };
 Return (
 <div className={styles.appHeader}>
 <Button variant=”primary” onClick={() => setModalOpen(true)}>
 Add Task
 </Button>
 <SelectButton
 Id=”status”
 onChange={€ => updateFilter€}
 value={filterStatus}
 >
 <option value=”all”>All</option>
 <option value=”incomplete”>Incomplete</option>
 <option value=”complete”>Completed</option>
 </SelectButton>
 <TodoModal type=”add” modalOpen={modalOpen} setModalOpen={setModalOpen} />
 </div>
 );
}
Export default AppHeader;
Button.js:
Import React from ‘react’;
Import styles from ‘../styles/modules/button.module.scss’;
Import { getClasses } from ‘../utils/getClasses’;
Const buttonTypes = {
 Primary: ‘primary’,
 Secondary: ‘secondary’,
};
Function Button({ type, variant = ‘primary’, children, …rest }) {
 Return (
 <button
 Type={type === ‘submit’ ? ‘submit’ : ‘button’}
 className={getClasses([
 styles.button,
 styles[`button--${buttonTypes[variant]}`],
 ])}
 {…rest}
 >
 {children}
 </button>
 );
}
Function SelectButton({ children, id, …rest }) {
 Return (
 <select
 Id={id}
 className={getClasses([styles.button, styles.button__select])}
 {…rest}
 >
 {children}
 </select>
 );
}
Export { SelectButton };
Export default Button;
Chechbutton.import { motion, useMotionValue, useTransform } from ‘framer-motion’;
Import React from ‘react’;
Import styles from ‘../styles/modules/todoItem.module.scss’;
Const checkVariants = {
 Initial: {
 Color: ‘#fff’,
 },
 Checked: { pathLength: 1 },
 Unchecked: { pathLength: 0 },
};
Const boxVariants = {
 Checked: {
 Background: ‘var(--primaryPurple)’,
 Transition: { duration: 0.1 },
 },
 Unchecked: { background: ‘var(--gray-2)’, transition: { duration: 0.1 } },
};
Function CheckButton({ checked, handleCheck }) {
 Const pathLength = useMotionValue(0);
 Const opacity = useTransform(pathLength, [0.05, 0.15], [0, 1]);
 Return (
 <motion.div
 Animate={checked ? ‘checked’ : ‘unchecked’}
 className={styles.svgBox}
 variants={boxVariants}
 onClick={() => handleCheck()}
 >
 <motion.svg
 className={styles.svg}
 viewBox=”0 0 53 38”
 fill=”none”
 xmlns=http://www.w3.org/2000/svg
 >
 <motion.path
 Variants={checkVariants}
 Animate={checked ? ‘checked’ : ‘unchecked’}
 Style={{ pathLength, opacity }}
 Fill=”none”
 strokeMiterlimit=”10”
 strokeWidth=”6”
 d=”M1.5 22L16 36.5L51.5 1”
 strokeLinejoin=”round”
 strokeLinecap=”round”
 />
 </motion.svg>
 </motion.div>
 );
}
Export default CheckButton;
Pagetitle.js:
Import React from ‘react’;
Import styles from ‘../styles/modules/title.module.scss’;
Function PageTitle({ children, …rest }) {
 Return (
 <p className={styles.title} {…rest}>
 {children}
 </p>
 );
}
Export default PageTitle;
Todo. Iteam.js:
Import { format } from ‘date-fns’;
Import { motion } from ‘framer-motion’;
Import toast from ‘react-hot-toast’;
Import React, { useEffect, useState } from ‘react’;
Import { MdDelete, MdEdit } from ‘react-icons/md’;
Import { useDispatch } from ‘react-redux’;
Import { deleteTodo, updateTodo } from ‘../slices/todoSlice’;
Import styles from ‘../styles/modules/todoItem.module.scss’;
Import { getClasses } from ‘../utils/getClasses’;
Import CheckButton from ‘./CheckButton’;
Import TodoModal from ‘./TodoModal’;
Const child = {
 Hidden: { y: 20, opacity: 0 },
 Visible: {
 Y: 0,
 Opacity: 1,
 },
};
Function TodoItem({ todo }) {
 Const dispatch = useDispatch();
 Const [checked, setChecked] = useState(false);
 Const [updateModalOpen, setUpdateModalOpen] = useState(false);
 useEffect(() => {
 if (todo.status === ‘complete’) {
 setChecked(true);
 } else {
 setChecked(false);
 }
 }, [todo.status]);
 Const handleCheck = () => {
 setChecked(!checked);
 dispatch(
 updateTodo({ …todo, status: checked ? ‘incomplete’ : ‘complete’ })
 );
 };
 Const handleDelete = () => {
 Dispatch(deleteTodo(todo.id));
 Toast.success(‘Todo Deleted Successfully’);
 };
 Const handleUpdate = () => {
 setUpdateModalOpen(true);
 };
 Return (
 <>
 <motion.div className={styles.item} variants={child}>
 <div className={styles.todoDetails}>
 <CheckButton checked={checked} handleCheck={handleCheck} />
 <div className={styles.texts}>
 <p
 className={getClasses([
 styles.todoText,
 todo.status === ‘complete’ && styles[‘todoText—completed’],
 ])}
 >
 {todo.title}
 </p>
 <p className={styles.time}>
 {format(new Date(todo.time), ‘p, MM/dd/yyyy’)}
 </p>
 </div>
 </div>
 <div className={styles.todoActions}>
 <div
 className={styles.icon}
 onClick={() => handleDelete()}
 onKeyDown={() => handleDelete()}
 tabIndex={0}
 role=”button”
 >
 <MdDelete />
 </div>
 <div
 className={styles.icon}
 onClick={() => handleUpdate()}
 onKeyDown={() => handleUpdate()}
 tabIndex={0}
 role=”button”
 >
 <MdEdit />
 </div>
 </div>
 </motion.div>
 <TodoModal
 Type=”update”
 modalOpen={updateModalOpen}
 setModalOpen={setUpdateModalOpen}
 todo={todo}
 />
 </>
 );
}
Export default TodoItem;
Todomodal.js:
Import React, { useEffect, useState } from ‘react’;
Import { v4 as uuid } from ‘uuid’;
Import { MdOutlineClose } from ‘react-icons/md’;
Import { useDispatch } from ‘react-redux’;
Import { AnimatePresence, motion } from ‘framer-motion’;
Import toast from ‘react-hot-toast’;
Import { addTodo, updateTodo } from ‘../slices/todoSlice’;
Import styles from ‘../styles/modules/modal.module.scss’;
Import Button from ‘./Button’;
Const dropIn = {
 Hidden: {
 Opacity: 0,
 Transform: ‘scale(0.9)’,
 },
 Visible: {
 Transform: ‘scale(1)’,
 Opacity: 1,
 Transition: {
 Duration: 0.1,
 Type: ‘spring’,
 Damping: 25,
 Stiffness: 500,
 },
 },
 Exit: {
 Transform: ‘scale(0.9)’,
 Opacity: 0,
 },
};
Function TodoModal({ type, modalOpen, setModalOpen, todo }) {
 Const dispatch = useDispatch();
 Const [title, setTitle] = useState(‘’);
 Const [status, setStatus] = useState(‘incomplete’);
 useEffect(() => {
 if (type === ‘update’ && todo) {
 setTitle(todo.title);
 setStatus(todo.status);
 } else {
 setTitle(‘’);
 setStatus(‘incomplete’);
 }
 }, [type, todo, modalOpen]);
 Const handleSubmit = € => {
 e.preventDefault();
 if (title === ‘’) {
 toast.error(‘Please enter a title’);
 return;
 }
 If (title && status) {
 If (type === ‘add’) {
 Dispatch(
 addTodo({
 id: uuid(),
 title,
 status,
 time: new Date().toLocaleString(),
 })
 );
 Toast.success(‘Task added successfully’);
 }
 If (type === ‘update’) {
 If (todo.title !== title || todo.status !== status) {
 Dispatch(updateTodo({ …todo, title, status }));
 Toast.success(‘Task Updated successfully’);
 } else {
 Toast.error(‘No changes made’);
 Return;
 }
 }
 setModalOpen(false);
 }
 };
 Return (
 <AnimatePresence>
 {modalOpen && (
 <motion.div
 className={styles.wrapper}
 initial={{ opacity: 0 }}
 animate={{ opacity: 1 }}
 exit={{ opacity: 0 }}
 >
 <motion.div
 className={styles.container}
 variants={dropIn}
 initial=”hidden”
 animate=”visible”
 exit=”exit”
 >
 <motion.div
 className={styles.closeButton}
 onKeyDown={() => setModalOpen(false)}
 onClick={() => setModalOpen(false)}
 role=”button”
 tabIndex={0}
 // animation
 Initial={{ top: 40, opacity: 0 }}
 Animate={{ top: -10, opacity: 1 }}
 Exit={{ top: 40, opacity: 0 }}
 >
 <MdOutlineClose />
 </motion.div>
 <form className={styles.form} onSubmit={€ => handleSubmit€}>
 <h1 className={styles.formTitle}>
 {type === ‘add’ ? ‘Add’ : ‘Update’} TODO
 </h1>
 <label htmlFor=”title”>
 Title
 <input
 Type=”text”
 Id=”title”
 Value={title}
 onChange={€ => setTitle(e.target.value)}
 />
 </label>
 <label htmlFor=”type”>
 Status
 <select
 Id=”type”
 Value={status}
 onChange={€ => setStatus(e.target.value)}
 >
 <option value=”incomplete”>Incomplete</option>
 <option value=”complete”>Completed</option>
 </select>
 </label>
 <div className={styles.buttonContainer}>
 <Button type=”submit” variant=”primary”>
 {type === ‘add’ ? ‘Add Task’ : ‘Update Task’}
 </Button>
 <Button variant=”secondary” onClick={() => setModalOpen(false)}>
 Cancel
 </Button>
 </div>
 </form>
 </motion.div>
 </motion.div>
 )}
 </AnimatePresence>
 );
}
Export default Todomodal;
Todoslice.js:
Import { createSlice } from ‘@reduxjs/toolkit’;
Const getInitialTodo = () => {
 // getting todo list
 Const localTodoList = window.localStorage.getItem(‘todoList’);
 // if todo list is not empty
 If (localTodoList) {
 Return JSON.parse(localTodoList);
 }
 Window.localStorage.setItem(‘todoList’, []);
 Return [];
};
Const initialValue = {
 filterStatus: ‘all’,
 todoList: getInitialTodo(),
};
Export const todoSlice = createSlice({
 Name: ‘todo’,
 initialState: initialValue,
 reducers: {
 addTodo: (state, action) => {
 state.todoList.push(action.payload);
 const todoList = window.localStorage.getItem(‘todoList’);
 if (todoList) {
 const todoListArr = JSON.parse(todoList);
 todoListArr.push({
 …action.payload,
 });
 Window.localStorage.setItem(‘todoList’, JSON.stringify(todoListArr));
 } else {
 Window.localStorage.setItem(
 ‘todoList’,
 JSON.stringify([
 {
 …action.payload,
 },
 ])
 );
 }
 },
 updateTodo: (state, action) => {
 const todoList = window.localStorage.getItem(‘todoList’);
 if (todoList) {
 const todoListArr = JSON.parse(todoList);
 todoListArr.forEach((todo) => {
 if (todo.id === action.payload.id) {
 todo.status = action.payload.status;
 todo.title = action.payload.title;
 }
 });
 Window.localStorage.setItem(‘todoList’, JSON.stringify(todoListArr));
 State.todoList = […todoListArr];
 }
 },
 deleteTodo: (state, action) => {
 const todoList = window.localStorage.getItem(‘todoList’);
 if (todoList) {
 const todoListArr = JSON.parse(todoList);
 todoListArr.forEach((todo, index) => {
 if (todo.id === action.payload) {
 todoListArr.splice(index, 1);
 }
 });
 Window.localStorage.setItem(‘todoList’, JSON.stringify(todoListArr));
 State.todoList = todoListArr;
 }
 },
 updateFilterStatus: (state, action) => {
 state.filterStatus = action.payload;
 },
 },
});
Export const { addTodo, updateTodo, deleteTodo, updateFilterStatus } =
 todoSlice.actions;
export default todoSlice.reducer;
App.module.scss:
@import ‘../_mixins.scss’;
.app__wrapper {
Max-width: 750px;
Width: 100%;
Margin: 0 auto;
}
.appHeader {
Display: flex;
Align-items: center;
Justify-content: space-between;
Height: 60px;
}
.content__wrapper {
Background-color: var(--bg-2);
Padding: 2rem;
Border-radius: 12px;
@include smallDeviceSize {
Padding: 1.5rem;
}
}
.emptyText {
// display: inline-block;
Font-size: 1.6rem;
Font-weight: 500;
Color: var(--black-2);
Text-align: center;
Margin: 0 auto;
Padding: 0.5rem 1rem;
Border-radius: 8px;
Background-color: var(--gray-2);
Width: max-content;
Height: auto;
}
Buttonmodule.css:
.button {
Display: inline-block;
Height: auto;
Padding: 0.8rem 2rem;
Border: none;
Border-radius: 6px;
Font-weight: 500;
Font-size: 1.6rem;
Text-decoration: none;
Text-transform: capitalize;
Cursor: pointer;
Overflow: hidden;
&__select {
Color: var(--black-2);
Font-family: Poppins;
Padding: 1rem;
Border: none;
Background-color: var(--bg-3);
Width: 150px;
Cursor: pointer;
}
}
.button—primary {
Background-color: var(--primaryPurple);
Color: var(--white);
}
.button—secondary {
Background-color: var(--bg-3);
Color: var(--black-1);
}
Modalbutton.js:
.wrapper {
Position: fixed;
Top: 0;
Left: 0;
Width: 100%;
Height: 100%;
Background: rgba(0, 0, 0, 0.5);
z-index: 1000;
display: flex;
justify-content: center;
align-items: center;
}
.container {
Background-color: var(--bg-2);
Max-width: 500px;
Width: 90%;
Margin: 0 auto;
Display: flex;
Justify-content: center;
Align-items: center;
Padding: 2rem;
Border-radius: 8px;
Position: relative;
}
.closeButton {
Position: absolute;
Top: -10px;
Right: 0;
Transform: translateY(-100%);
Font-size: 2.5rem;
Padding: 0.5rem;
Border-radius: 4px;
Background-color: var(--gray-1);
Color: var(--black-2);
Display: flex;
Align-items: center;
Justify-content: center;
Cursor: pointer;
Transition: 0.3s ease all;
z-index: -1;
&:hover {
Background-color: #e32525;
Color: white;
}
}
.formTitle {
Color: var(--black-1);
Font-size: 2rem;
Font-weight: 600;
Margin-bottom: 2rem;
Text-transform: capitalize;
}
.form {
Width: 100%;
Label {
Font-size: 1.6rem;
Color: var(--black-1);
Input,
Select {
Margin-top: 0.5rem;
Margin-bottom: 2rem;
Width: 100%;
Padding: 1rem;
Border: none;
Background-color: var(--white);
Font-size: 1.6rem;
}
}
}
.buttonContainer {
Display: flex;
Justify-content: flex-start;
Align-items: center;
Margin-top: 2rem;
Gap: 1rem;
}
Titlemodule.js:
@import ‘../mixins’;
.title {
Display: inline-block;
Width: 100%;
Font-family: ‘Poppins’;
Font-size: 4rem;
Font-weight: 700;
Text-transform: uppercase;
Text-align: center;
Margin: 0 auto;
Margin-top: 2rem;
Margin-bottom: 1.5rem;
Color: var(--black-1);
@include smallDeviceSize {
Font-size: 3rem;
}
}
Todoitemmodule.js:
Import ‘../_mixins.scss’;
.item {
Display: flex;
Align-items: center;
Justify-content: space-between;
Padding: 1rem;
Background: var(--white);
Margin-bottom: 1.5rem;
Border-radius: 4px;
&:last-child {
Margin-bottom: 0;
}
}
.todoDetails {
Display: flex;
Align-items: center;
Justify-content: flex-start;
Gap: 1rem;
}
.svgBox {
Flex-basis: 25px;
Flex-shrink: 0;
Height: 25px;
Border-radius: 2px;
Display: flex;
Align-items: center;
Justify-content: center;
Padding: 5px;
Cursor: pointer;
Transition: 0.3s ease background-color;
&:hover {
Background-color: var(--grey-2);
}
Svg {
Width: 100%;
Height: 100%;
Stroke: white;
Display: flex;
Align-items: center;
Justify-content: center;
}
}
.texts {
Display: flex;
Flex-direction: column;
Overflow: hidden;
}
.todoText {
Word-break: break-all;
Font-weight: 500;
Font-size: 1.4rem;
Color: var(--black-2);
&--completed {
Text-decoration: line-through;
Opacity: 0.7;
}
}
.time {
Display: block;
Font-size: 1.2rem;
Font-weight: 300;
Margin-top: -0.2rem;
Color: var(--black-2);
}
.todoActions {
Display: flex;
Align-items: center;
Justify-content: center;
Gap: 1rem;
}
.icon {
Font-size: 2rem;
Padding: 0.5rem;
Border-radius: 4px;
Background-color: var(--gray-1);
Color: var(--black-2);
Display: flex;
Align-items: center;
Justify-content: center;
Cursor: pointer;
Transition: 0.3s ease background-color;
&:hover {
Background-color: var(--gray-2);
}
}
App.js:
Import React from ‘react’;
Import { Toaster } from ‘react-hot-toast’;
Import AppContent from ‘./components/AppContent’;
Import AppHeader from ‘./components/AppHeader’;
Import PageTitle from ‘./components/PageTitle’;
Import styles from ‘./styles/modules/app.module.scss’;
Function App() {
 Return (
 <>
 <div className=”container”>
 <PageTitle>TODO List</PageTitle>
 <div className={styles.app__wrapper}>
 <AppHeader />
 <AppContent />
 </div>
 </div>
 <Toaster
 Position=”bottom-right”
 toastOptions={{
 style: {
 fontSize: ‘1.4rem’,
 },
 }}
 />
 </>
 );
}
Export default App;
Index.js:
Import React from ‘react’;
Import ReactDOM from ‘react-dom’;
Import { Provider } from ‘react-redux’;
Import App from ‘./App’;
Import ‘@fontsource/poppins’;
Import ‘@fontsource/poppins/500.css’;
Import ‘@fontsource/poppins/600.css’;
Import ‘@fontsource/poppins/700.css’;
Import ‘./styles/GlobalStyles.css’;
Import { store } from ‘./app/store’;
ReactDOM.render(
 <React.StrictMode>
 <Provider store={store}>
 <App />
 </Provider>
 </React.StrictMode>,
 Document.getElementById(‘root’)
);
```

## RESULT:
Thus s fully functional to do list was created.
