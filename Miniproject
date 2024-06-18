import tkinter as tk
from tkinter import Menu, Text, Scrollbar, simpledialog
from tkinter.messagebox import showinfo
from tkinter.filedialog import askopenfilename, asksaveasfilename
import os

class Notepad:
    def __init__(self, root, **kwargs):
        self._root = root
        self._root.title("Untitled - Notepad")
        self._root.geometry(f"{kwargs.get('width', 600)}x{kwargs.get('height', 400)}")
        self._file = None
        self._recent_files = []
        self._find_string = None

        self._dark_theme = False
        self._bold = False
        self._italic = False
        self._heading = False
        self._subscript = False
        self._superscript = False

        self._setup_ui()
        self._setup_menu()
        self._setup_status_bar()

    def _setup_ui(self):
        """Sets up the main UI components."""
        self._text_area = Text(self._root, undo=True, wrap='word')
        self._text_area.grid(row=0, column=0, sticky=tk.N + tk.E + tk.S + tk.W)
        
        self._scrollbar = Scrollbar(self._root)
        self._scrollbar.grid(row=0, column=1, sticky=tk.N + tk.S)
        self._scrollbar.config(command=self._text_area.yview)
        self._text_area.config(yscrollcommand=self._scrollbar.set)
        
        self._root.grid_rowconfigure(0, weight=1)
        self._root.grid_columnconfigure(0, weight=1)

    def _setup_menu(self):
        """Sets up the menu bar with File, Edit, Format, View, and Help menus."""
        self._menu_bar = Menu(self._root)

        # File Menu
        file_menu = Menu(self._menu_bar, tearoff=0)
        file_menu.add_command(label="New", command=self._new_file)
        file_menu.add_command(label="Open", command=self._open_file)
        file_menu.add_command(label="Save", command=self._save_file)
        file_menu.add_separator()
        self._recent_files_menu = Menu(file_menu, tearoff=0)
        file_menu.add_cascade(label="Recent Files", menu=self._recent_files_menu)
        file_menu.add_separator()
        file_menu.add_command(label="Exit", command=self._quit_application)
        self._menu_bar.add_cascade(label="File", menu=file_menu)

        # Edit Menu
        edit_menu = Menu(self._menu_bar, tearoff=0)
        edit_menu.add_command(label="Undo", command=self._undo)
        edit_menu.add_command(label="Redo", command=self._redo)
        edit_menu.add_separator()
        edit_menu.add_command(label="Cut", command=self._cut)
        edit_menu.add_command(label="Copy", command=self._copy)
        edit_menu.add_command(label="Paste", command=self._paste)
        edit_menu.add_command(label="Find", command=self._find)
        edit_menu.add_command(label="Find Next", command=self._find_next)
        edit_menu.add_command(label="Replace", command=self._replace)
        edit_menu.add_command(label="Replace All", command=self._replace_all)
        self._menu_bar.add_cascade(label="Edit", menu=edit_menu)

        # Format Menu
        format_menu = Menu(self._menu_bar, tearoff=0)
        format_menu.add_command(label="Word Wrap", command=self._toggle_word_wrap)
        format_menu.add_command(label="Font...", command=self._choose_font)
        format_menu.add_separator()
        format_menu.add_command(label="Bold", command=self._toggle_bold)
        format_menu.add_command(label="Italic", command=self._toggle_italic)
        format_menu.add_command(label="Subscript", command=self._toggle_subscript)
        format_menu.add_command(label="Superscript", command=self._toggle_superscript)
        format_menu.add_command(label="Heading", command=self._toggle_heading)
        format_menu.add_command(label="Paragraph", command=self._toggle_paragraph)
        self._menu_bar.add_cascade(label="Format", menu=format_menu)

        # View Menu
        view_menu = Menu(self._menu_bar, tearoff=0)
        view_menu.add_command(label="Status Bar", command=self._toggle_status_bar)
        view_menu.add_separator()
        view_menu.add_command(label="Dark Theme", command=self._toggle_dark_theme)
        self._menu_bar.add_cascade(label="View", menu=view_menu)

        # Help Menu
        help_menu = Menu(self._menu_bar, tearoff=0)
        help_menu.add_command(label="About Notepad", command=self._show_about)
        self._menu_bar.add_cascade(label="Help", menu=help_menu)

        self._root.config(menu=self._menu_bar)

    def _setup_status_bar(self):
        """Sets up the status bar to display line and column numbers."""
        self._status_bar = tk.Label(self._root, text="Line 1, Column 1", bd=1, relief=tk.SUNKEN, anchor=tk.W)
        self._status_bar.grid(row=1, column=0, sticky=tk.E + tk.W)
        self._text_area.bind("<KeyRelease>", self._update_status_bar)

    def _update_status_bar(self, event=None):
        """Updates the status bar with current line and column numbers."""
        row, col = self._text_area.index(tk.INSERT).split('.')
        words = len(self._text_area.get(1.0, 'end-1c').split())
        chars = len(self._text_area.get(1.0, 'end-1c'))
        self._status_bar.config(text=f"Line {int(row)}, Column {int(col) + 1} | Words: {words} | Chars: {chars}")

    def _toggle_word_wrap(self):
        """Toggles word wrap on and off."""
        wrap_mode = self._text_area.cget('wrap')
        self._text_area.config(wrap='none' if wrap_mode == 'word' else 'word')

    def _choose_font(self):
        """Opens a dialog to choose font and size."""
        font_name = simpledialog.askstring("Font", "Enter font name (e.g., Arial):")
        font_size = simpledialog.askinteger("Font Size", "Enter font size:", minvalue=8, maxvalue=72)
        if font_name and font_size:
            self._text_area.config(font=(font_name, font_size))

    def _toggle_status_bar(self):
        """Toggles the visibility of the status bar."""
        if self._status_bar.winfo_ismapped():
            self._status_bar.grid_remove()
        else:
            self._status_bar.grid()

    def _new_file(self):
        """Creates a new file."""
        self._root.title("Untitled - Notepad")
        self._file = None
        self._text_area.delete(1.0, tk.END)

    def _open_file(self):
        """Opens an existing file."""
        self._file = askopenfilename(defaultextension=".txt", filetypes=[("All Files", "."), ("Text Documents", "*.txt")])
        
        if self._file:
            self._root.title(os.path.basename(self._file) + " - Notepad")
            with open(self._file, "r") as file:
                self._text_area.delete(1.0, tk.END)
                self._text_area.insert(1.0, file.read())
            self._add_recent_file(self._file)

    def _save_file(self):
        """Saves the current file."""
        if not self._file:
            self._file = asksaveasfilename(initialfile='Untitled.txt', defaultextension=".txt",
                                           filetypes=[("All Files", "."), ("Text Documents", "*.txt")])
        if self._file:
            with open(self._file, "w") as file:
                file.write(self._text_area.get(1.0, tk.END))
            self._root.title(os.path.basename(self._file) + " - Notepad")
            self._add_recent_file(self._file)

    def _cut(self):
        self._text_area.event_generate("<<Cut>>")

    def _copy(self):
        self._text_area.event_generate("<<Copy>>")

    def _paste(self):
        self._text_area.event_generate("<<Paste>>")

    def _undo(self):
        self._text_area.event_generate("<<Undo>>")

    def _redo(self):
        self._text_area.event_generate("<<Redo>>")

    def _find(self):
        """Finds a string in the text area."""
        self._find_string = simpledialog.askstring("Find", "Enter text to find:")
        self._find_next()

    def _find_next(self):
        """Finds the next occurrence of the string."""
        if self._find_string:
            start_pos = self._text_area.index(tk.INSERT)
            self._text_area.tag_remove("highlight", "1.0", tk.END)
            pos = self._text_area.search(self._find_string, start_pos, stopindex=tk.END)
            if pos:
                end_pos = f"{pos}+{len(self._find_string)}c"
                self._text_area.tag_add("highlight", pos, end_pos)
                self._text_area.tag_config("highlight", background="yellow")
                self._text_area.mark_set(tk.INSERT, end_pos)
                self._text_area.see(tk.INSERT)

    def _replace(self):
        """Finds and replaces a string in the text area."""
        find_string = simpledialog.askstring("Find", "Enter text to find:")
        replace_string = simpledialog.askstring("Replace", "Enter text to replace with:")
        if find_string and replace_string:
            start_pos = self._text_area.search(find_string, "1.0", stopindex=tk.END)
            if start_pos:
                end_pos = f"{start_pos}+{len(find_string)}c"
                self._text_area.delete(start_pos, end_pos)
                self._text_area.insert(start_pos, replace_string)

    def _replace_all(self):
        """Finds and replaces all occurrences of a string in the text area."""
        find_string = simpledialog.askstring("Find", "Enter text to find:")
        replace_string = simpledialog.askstring("Replace", "Enter text to replace with:")
        if find_string and replace_string:
            start_pos = "1.0"
            while True:
                start_pos = self._text_area.search(find_string, start_pos, stopindex=tk.END)
                if not start_pos:
                    break
                end_pos = f"{start_pos}+{len(find_string)}c"
                self._text_area.delete(start_pos, end_pos)
                self._text_area.insert(start_pos, replace_string)
                start_pos = end_pos

    def _toggle_dark_theme(self):
        """Toggles dark theme."""
        self._dark_theme = not self._dark_theme
        theme = "black" if self._dark_theme else "white"
        text_color = "white" if self._dark_theme else "black"
        self._root.config(bg=theme)
        self._text_area.config(bg=theme, fg=text_color)
        self._status_bar.config(bg=theme, fg=text_color)

    def _toggle_bold(self):
        """Toggles bold text."""
        self._bold = not self._bold
        current_tags = self._text_area.tag_names("sel.first")
        if "bold" in current_tags:
            self._text_area.tag_remove("bold", "sel.first", "sel.last")
        else:
            self._text_area.tag_add("bold", "sel.first", "sel.last")
            self._text_area.tag_configure("bold", font=('Arial', 12, 'bold'))

    def _toggle_italic(self):
        """Toggles italic text."""
        self._italic = not self._italic
        current_tags = self._text_area.tag_names("sel.first")
        if "italic" in current_tags:
            self._text_area.tag_remove("italic", "sel.first", "sel.last")
        else:
            self._text_area.tag_add("italic", "sel.first", "sel.last")
            self._text_area.tag_configure("italic", font=('Arial', 12, 'italic'))

    def _toggle_subscript(self):
        """Toggles subscript."""
        self._subscript = not self._subscript
        current_tags = self._text_area.tag_names("sel.first")
        if "subscript" in current_tags:
            self._text_area.tag_remove("subscript", "sel.first", "sel.last")
        else:
            self._text_area.tag_add("subscript", "sel.first", "sel.last")
            self._text_area.tag_configure("subscript", offset=-4)

    def _toggle_superscript(self):
        """Toggles superscript."""
        self._superscript = not self._superscript
        current_tags = self._text_area.tag_names("sel.first")
        if "superscript" in current_tags:
            self._text_area.tag_remove("superscript", "sel.first", "sel.last")
        else:
            self._text_area.tag_add("superscript", "sel.first", "sel.last")
            self._text_area.tag_configure("superscript", offset=4)

    def _toggle_heading(self):
        """Sets selected text as heading."""
        self._heading = True
        current_tags = self._text_area.tag_names("sel.first")
        if "heading" in current_tags:
            self._text_area.tag_remove("heading", "sel.first", "sel.last")
        else:
            self._text_area.tag_add("heading", "sel.first", "sel.last")
            self._text_area.tag_configure("heading", font=('Arial', 16, 'bold'))

    def _toggle_paragraph(self):
        """Sets selected text as paragraph."""
        self._heading = False
        current_tags = self._text_area.tag_names("sel.first")
        if "paragraph" in current_tags:
            self._text_area.tag_remove("paragraph", "sel.first", "sel.last")
        else:
            self._text_area.tag_add("paragraph", "sel.first", "sel.last")
            self._text_area.tag_configure("paragraph", font=('Arial', 12))

    def _add_recent_file(self, file_path):
        """Adds a file to the recent files menu."""
        if file_path not in self._recent_files:
            self._recent_files.append(file_path)
            self._update_recent_files_menu()

    def _update_recent_files_menu(self):
        """Updates the recent files menu."""
        self._recent_files_menu.delete(0, tk.END)
        for file_path in self._recent_files[-5:]:
            self._recent_files_menu.add_command(label=file_path, command=lambda path=file_path: self._open_recent_file(path))

    def _open_recent_file(self, file_path):
        """Opens a file from the recent files menu."""
        if os.path.exists(file_path):
            self._file = file_path
            self._root.title(os.path.basename(self._file) + " - Notepad")
            with open(self._file, "r") as file:
                self._text_area.delete(1.0, tk.END)
                self._text_area.insert(1.0, file.read())
            self._add_recent_file(self._file)

    def _quit_application(self):
        self._root.destroy()

    def _show_about(self):
        showinfo("Notepad", "Notepad application created using Tkinter\nVersion 1.0")

    def run(self):
        """Runs the main application."""
        self._root.mainloop()

if __name__ == "__main__":
    root = tk.Tk()
    notepad = Notepad(root, width=600, height=400)
    notepad.run()
