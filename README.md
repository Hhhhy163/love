import streamlit as st

# إعدادات الصفحة
st.set_page_config(page_title="توصيل الطلبات", page_icon="🍽️", layout="wide")

# تهيئة session state
if 'cart' not in st.session_state:
    st.session_state.cart = []
if 'page' not in st.session_state:
    st.session_state.page = 'home'
if 'selected_restaurant' not in st.session_state:
    st.session_state.selected_restaurant = None

# بيانات المطاعم
restaurants = [
    {
        'name': 'مطعم البيت الدمشقي',
        'image': '🍽️',
        'rating': 4.5,
        'delivery_time': '30-40 دقيقة',
        'category': 'طعام عربي'
    },
    {
        'name': 'بيتزا هت',
        'image': '🍕',
        'rating': 4.2,
        'delivery_time': '25-35 دقيقة',
        'category': 'إيطالي'
    },
    {
        'name': 'برجر كينج',
        'image': '🍔',
        'rating': 4.0,
        'delivery_time': '20-30 دقيقة',
        'category': 'وجبات سريعة'
    },
    {
        'name': 'سوشي ماستر',
        'image': '🍱',
        'rating': 4.7,
        'delivery_time': '35-45 دقيقة',
        'category': 'ياباني'
    },
]

# بيانات قائمة الطعام
menu_items = [
    {'name': 'برجر لحم', 'price': 45.0, 'image': '🍔', 'description': 'برجر لحم طازج مع الخضار'},
    {'name': 'بيتزا مارجريتا', 'price': 60.0, 'image': '🍕', 'description': 'بيتزا بالجبن والريحان'},
    {'name': 'شاورما دجاج', 'price': 35.0, 'image': '🌯', 'description': 'شاورما دجاج مع الثوم'},
    {'name': 'سلطة يونانية', 'price': 25.0, 'image': '🥗', 'description': 'سلطة طازجة بالجبن الفيتا'},
    {'name': 'بطاطس مقلية', 'price': 15.0, 'image': '🍟', 'description': 'بطاطس ذهبية مقرمشة'},
    {'name': 'مشروب بارد', 'price': 10.0, 'image': '🥤', 'description': 'مشروب غازي منعش'},
]

# CSS مخصص
st.markdown("""
<style>
    .main {
        direction: rtl;
        text-align: right;
    }
    .restaurant-card {
        background-color: white;
        padding: 20px;
        border-radius: 10px;
        box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        margin: 10px 0;
    }
    .menu-item-card {
        background-color: white;
        padding: 15px;
        border-radius: 10px;
        box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        text-align: center;
    }
    .cart-total {
        background-color: #4CAF50;
        color: white;
        padding: 20px;
        border-radius: 10px;
        font-size: 24px;
        font-weight: bold;
    }
</style>
""", unsafe_allow_html=True)


# الصفحة الرئيسية
def home_page():
    st.title("🍽️ خدمة التوصيل")

    # شريط البحث
    search = st.text_input("🔍 ابحث عن مطعم...", key="search")

    st.header("المطاعم المتاحة")

    # عرض المطاعم
    for restaurant in restaurants:
        col1, col2 = st.columns([1, 4])

        with col1:
            st.markdown(f"<div style='font-size: 60px; text-align: center;'>{restaurant['image']}</div>",
                        unsafe_allow_html=True)

        with col2:
            st.markdown(f"### {restaurant['name']}")
            st.write(f"**{restaurant['category']}**")
            st.write(f"⭐ {restaurant['rating']} | ⏰ {restaurant['delivery_time']}")

            if st.button(f"عرض القائمة", key=f"btn_{restaurant['name']}"):
                st.session_state.selected_restaurant = restaurant['name']
                st.session_state.page = 'menu'
                st.rerun()

        st.divider()


# صفحة القائمة
def menu_page():
    col1, col2 = st.columns([4, 1])

    with col1:
        st.title(f"📋 {st.session_state.selected_restaurant}")

    with col2:
        if st.button("🏠 الرئيسية"):
            st.session_state.page = 'home'
            st.rerun()

        cart_count = len(st.session_state.cart)
        if st.button(f"🛒 السلة ({cart_count})"):
            st.session_state.page = 'cart'
            st.rerun()

    st.divider()

    # عرض الأصناف في شبكة
    cols = st.columns(3)

    for idx, item in enumerate(menu_items):
        with cols[idx % 3]:
            st.markdown(f"<div class='menu-item-card'>", unsafe_allow_html=True)
            st.markdown(f"<div style='font-size: 80px;'>{item['image']}</div>", unsafe_allow_html=True)
            st.markdown(f"### {item['name']}")
            st.write(item['description'])
            st.markdown(f"**{item['price']} ج.م**")

            if st.button("➕ إضافة للسلة", key=f"add_{idx}"):
                st.session_state.cart.append(item)
                st.success("✅ تمت الإضافة إلى السلة")
                st.rerun()

            st.markdown("</div>", unsafe_allow_html=True)
            st.write("")


# صفحة السلة
def cart_page():
    col1, col2 = st.columns([4, 1])

    with col1:
        st.title("🛒 سلة المشتريات")

    with col2:
        if st.button("🏠 الرئيسية"):
            st.session_state.page = 'home'
            st.rerun()

        if st.button("📋 القائمة"):
            st.session_state.page = 'menu'
            st.rerun()

    st.divider()

    if not st.session_state.cart:
        st.markdown("<div style='text-align: center; padding: 50px;'>", unsafe_allow_html=True)
        st.markdown("<div style='font-size: 100px;'>🛒</div>", unsafe_allow_html=True)
        st.markdown("### السلة فارغة")
        st.markdown("</div>", unsafe_allow_html=True)
    else:
        # عرض العناصر
        for idx, item in enumerate(st.session_state.cart):
            col1, col2, col3, col4 = st.columns([1, 3, 2, 1])

            with col1:
                st.markdown(f"<div style='font-size: 40px;'>{item['image']}</div>", unsafe_allow_html=True)

            with col2:
                st.write(f"**{item['name']}**")
                st.write(item['description'])

            with col3:
                st.write(f"**{item['price']} ج.م**")

            with col4:
                if st.button("🗑️", key=f"remove_{idx}"):
                    st.session_state.cart.pop(idx)
                    st.rerun()

            st.divider()

        # حساب الإجمالي
        total = sum(item['price'] for item in st.session_state.cart)

        st.markdown(f"""
        <div class='cart-total'>
            الإجمالي: {total:.2f} ج.م
        </div>
        """, unsafe_allow_html=True)

        st.write("")

        col1, col2, col3 = st.columns([1, 2, 1])
        with col2:
            if st.button("✅ تأكيد الطلب", use_container_width=True):
                st.success("🎉 تم تأكيد طلبك! سيصل خلال 30-40 دقيقة")
                st.session_state.cart = []
                st.balloons()
                st.session_state.page = 'home'
                st.rerun()


# التنقل بين الصفحات
if st.session_state.page == 'home':
    home_page()
elif st.session_state.page == 'menu':
    menu_page()
elif st.session_state.page == 'cart':
    cart_page()
