import streamlit as st
from streamlit_option_menu import option_menu
import os
import base64

# --- 1. MODULARE IMPORTE ---
from src.views import dashboard

# --- INITIALER STATE & ROUTING ENGINE ---
if 'theme' not in st.session_state:
    st.session_state.theme = 'dark'
if 'current_page' not in st.session_state:
    st.session_state.current_page = "Dashboard"
if 'last_menu' not in st.session_state:
    st.session_state.last_menu = "Dashboard"

st.set_page_config(page_title="B&F OS", layout="wide", initial_sidebar_state="expanded")

# --- FARB-DEFINITIONEN ---
if st.session_state.theme == 'dark':
    bg_color = "#0c0c0f"
    text_color = "#ffffff"
    border_color = "#1e1e26"
    menu_hover = "rgba(139, 92, 246, 0.15)"
    bottom_bg = "#16161a"
else:
    bg_color = "#ffffff"
    text_color = "#000000"
    border_color = "#dee2e6"
    menu_hover = "rgba(139, 92, 246, 0.1)"
    bottom_bg = "#f8f9fa"

# --- LOGO FUNKTION ---
def get_base64_logo():
    paths_to_try = ["src/views/softwarelogo.png", "src/views/software.png", "software.png"]
    for p in paths_to_try:
        if os.path.exists(p):
            with open(p, "rb") as f:
                return base64.b64encode(f.read()).decode()
    return None

logo_b64 = get_base64_logo()

# --- CSS: THE ULTIMATE OVERRIDE ---
st.markdown(f"""
    <style>
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@500;600;700&display=swap');

    /* 1. Sidebar starr machen */
    [data-testid="stSidebar"] {{
        background-color: {bg_color} !important;
        min-width: 260px !important; 
        max-width: 260px !important;
        border-right: 1px solid {border_color} !important;
    }}
    [data-testid="stSidebarResizer"] {{ display: none !important; }}
    [data-testid="stSidebarContent"], [data-testid="stSidebarNav"], [data-testid="stSidebarUserContent"],
    .st-emotion-cache-16idsys, .st-emotion-cache-6qob1r {{
        background-color: transparent !important;
    }}
    .stApp {{
        background-color: {bg_color} !important;
        color: {text_color} !important;
        font-family: 'Inter', sans-serif !important;
    }}

    /* 2. UNSICHTBARE BUTTONS (Genau positioniert über der UI) */
    /* Theme Switcher (Top Right) */
    [data-testid="stSidebar"] [data-testid="stElementContainer"]:nth-child(1) button {{
        position: fixed; top: 20px; right: 150px; width: 100px; height: 40px; opacity: 0; z-index: 1000002; cursor: pointer;
    }}
    /* Account Button (Bottom Left) */
    [data-testid="stSidebar"] [data-testid="stElementContainer"]:nth-child(2) button {{
        position: fixed; bottom: 20px; left: 20px; width: 110px; height: 50px; opacity: 0; z-index: 1000002; cursor: pointer;
    }}
    /* Settings Button (Bottom Left) */
    [data-testid="stSidebar"] [data-testid="stElementContainer"]:nth-child(3) button {{
        position: fixed; bottom: 20px; left: 130px; width: 110px; height: 50px; opacity: 0; z-index: 1000002; cursor: pointer;
    }}

    /* 3. Logo absolut festnageln */
    .tradezella-logo {{
        position: fixed; top: 25px; left: 25px; width: 140px; z-index: 1000000; pointer-events: none;
    }}

    /* 4. Top Nav Design */
    .top-nav-container {{
        position: fixed; top: 20px; right: 40px; display: flex; gap: 15px; z-index: 1000001;
    }}
    .theme-design {{
        background: #16161a; border: 1px solid #30363d; border-radius: 8px; display: flex; padding: 4px; pointer-events: none;
    }}

    /* 5. Bottom Bar (Visible Design) */
    .bottom-bar-bg {{
        position: fixed; bottom: 20px; left: 20px; width: 220px; display: flex;
        background: {bottom_bg}; border-radius: 10px; border: 1px solid {border_color};
        z-index: 99999; padding: 12px 0px;
    }}
    .bottom-icon {{
        color: #a1a1aa; font-size: 13px; font-weight: 600; display: flex; align-items: center; justify-content: center; gap: 8px; flex: 1; transition: 0.2s; pointer-events: none;
    }}
    .bottom-icon:hover {{ color: {text_color}; }}

    /* Sidebar Links Hover */
    .nav-link {{ background-color: transparent !important; color: #a1a1aa !important; font-weight: 600 !important; border-radius: 8px !important; margin-bottom: 4px !important; }}
    .nav-link:hover {{ border: 1px solid #8b5cf6 !important; background-color: {menu_hover} !important; color: #ffffff !important; }}
    header, .stDeployButton, [data-testid="stSidebarCollapseButton"] {{ display: none !important; }}
    </style>
    """, unsafe_allow_html=True)

# --- TOP NAV ANZEIGE ---
st.markdown(f"""
    <div class="top-nav-container">
        <div class="theme-design">
            <div style="padding:4px 12px; border-radius:6px; font-size:11px; font-weight:700; background:{'white' if st.session_state.theme=='dark' else 'transparent'}; color:{'black' if st.session_state.theme=='dark' else '#555'};">DARK</div>
            <div style="padding:4px 12px; border-radius:6px; font-size:11px; font-weight:700; background:{'white' if st.session_state.theme=='light' else 'transparent'}; color:{'black' if st.session_state.theme=='light' else '#555'};">LIGHT</div>
        </div>
        <div style="background:white; color:black; padding:8px 18px; border-radius:8px; font-weight:700; font-size:14px; cursor:pointer;">Sign In</div>
    </div>
    """, unsafe_allow_html=True)

# --- SIDEBAR ENGINE ---
with st.sidebar:
    # 🚨 WICHTIG: Diese 3 Buttons MÜSSEN ganz oben stehen (für das CSS Targeting)
    theme_btn = st.button("ThemeHack")
    acc_btn = st.button("AccHack")
    set_btn = st.button("SetHack")

    # Logo
    if logo_b64:
        st.markdown(f'<div class="tradezella-logo"><img src="data:image/png;base64,{logo_b64}" style="width:100%;"></div>', unsafe_allow_html=True)
    
    st.markdown("<div style='height: 110px;'></div>", unsafe_allow_html=True)

    # Hauptmenü (Ohne Settings)
    main_menu_items = ["Dashboard", "Challenges", "Journal", "Steuern & Finanzen"]
    def_index = main_menu_items.index(st.session_state.current_page) if st.session_state.current_page in main_menu_items else 0
    
    selected = option_menu(
        menu_title=None,
        options=main_menu_items,
        icons=["grid-fill", "shield-fill-check", "calendar2-check-fill", "bank2"],
        default_index=def_index,
        styles={
            "container": {"background-color": "transparent", "padding": "0!important"},
            "icon": {"color": "white", "font-size": "18px"},
            "nav-link": {"font-size": "15px", "text-align": "left", "margin":"0px"},
            "nav-link-selected": {"background-color": "#8b5cf6", "color": "white", "font-weight": "700"}
        }
    )

    # Bottom Bar HTML (Account & Settings)
    st.markdown(f"""
        <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.1/font/bootstrap-icons.css">
        <div class="bottom-bar-bg">
            <div class="bottom-icon" style="border-right: 1px solid {border_color};"><i class="bi bi-person-fill" style="font-size: 16px;"></i> Account</div>
            <div class="bottom-icon"><i class="bi bi-gear-fill" style="font-size: 16px;"></i> Settings</div>
        </div>
    """, unsafe_allow_html=True)

# --- EVENT & STATE HANDLING ---
# Theme Switch
if theme_btn:
    st.session_state.theme = "light" if st.session_state.theme == "dark" else "dark"
    st.rerun()

# Menü Klicks abfangen
if selected != st.session_state.last_menu:
    st.session_state.current_page = selected
    st.session_state.last_menu = selected
    st.rerun()

# Bottom Bar Klicks abfangen
if acc_btn:
    st.session_state.current_page = "Account"
    st.rerun()
if set_btn:
    st.session_state.current_page = "Settings"
    st.rerun()

# --- CONTENT ROUTING ---
if st.session_state.current_page == "Dashboard":
    dashboard.render(st.session_state.theme, text_color)
elif st.session_state.current_page == "Account":
    st.markdown("<div style='height: 80px;'></div>", unsafe_allow_html=True)
    st.title("👤 Account Hub")
    st.write("Verwalte deine Profil-Daten, Limits und API-Keys.")
elif st.session_state.current_page == "Settings":
    st.markdown("<div style='height: 80px;'></div>", unsafe_allow_html=True)
    st.title("⚙️ System Settings")
    st.write("Passe hier das Verhalten der Trading Engine an.")
else:
    st.markdown("<div style='height: 80px;'></div>", unsafe_allow_html=True)
    st.title(f"B&F Terminal - {st.session_state.current_page}")
    st.write("Sektion im Aufbau.")
