<script>
  import { tick, onDestroy, onMount } from 'svelte'

  const siteName = 'Chez Mario pizzeria'
  const locales = [
    { id: 'fr', label: '🇫🇷' },
    { id: 'en', label: '🇬🇧' },
    { id: 'it', label: '🇮🇹' }
  ]
  let locale = 'fr'

  /** Hero portrait — swap for your own slice-of-life shot. */
  const heroImageUrl = '/oven.jpg'
  const heroImageAlt = 'Wood-fired oven with pizza baking inside'

  /** Update these when you have the real address — map links are generated from `venueMapsQuery`. */
  const venueStreet = '13 avenue de la Libération'
  const venueCityLine = '42680 Saint-Marcellin-en-Forez, France'
  const venueMapsQuery = `${venueStreet}, ${venueCityLine}`
  const mapsEmbedUrl = `https://maps.google.com/maps?q=${encodeURIComponent(venueMapsQuery)}&z=16&output=embed`
  const mapsOpenUrl = `https://www.google.com/maps/search/?api=1&query=${encodeURIComponent(venueMapsQuery)}`

  const img = (/** @type {string} */ id, /** @type {number} */ w = 900) =>
    `https://images.unsplash.com/${id}?auto=format&fit=crop&w=${w}&q=82`

  const categories = [
    {
      id: 'tomato',
      label: 'Tomato',
      title: 'Tomato bases',
      note: 'Slow-cooked passata, bright acidity, classic pairings.',
      pizzas: [
        {
          name: 'Margherita',
          ingredients: ['San Marzano tomato', 'Fior di latte', 'Basil', 'EVO'],
          image: img('photo-1604382355076-af4b0eb60143')
        },
        {
          name: 'Pepperoni',
          ingredients: ['Tomato sauce', 'Mozzarella', 'Pepperoni', 'Oregano'],
          image: img('photo-1628840042765-356cda07504e')
        },
        {
          name: 'Marinara',
          ingredients: ['San Marzano tomato', 'Garlic', 'Oregano', 'EVO', 'No cheese'],
          image: img('photo-1751200884901-c1c6f43ae1d6')
        },
        {
          name: 'Napoli',
          ingredients: ['Tomato sauce', 'Mozzarella', 'Anchovies', 'Capers', 'Olives', 'Oregano'],
          image: img('photo-1765629315623-ea6f41a8bada')
        },
        {
          name: 'Giardino',
          ingredients: ['Tomato sauce', 'Mozzarella', 'Peppers', 'Courgette', 'Aubergine', 'Basil'],
          image: img('photo-1594007654729-407eedc4be65')
        },
        {
          name: 'Diavola',
          ingredients: ['Tomato sauce', 'Mozzarella', 'Spicy salami', 'Chili', 'Hot honey'],
          image: img('photo-1574071318508-1cdbab80d002')
        },
        {
          name: 'Hawaiian',
          ingredients: ['Tomato sauce', 'Mozzarella', 'Ham', 'Roasted pineapple', 'Spring onion'],
          image: img('photo-1562835155-a7c2a225e97d')
        },
        {
          name: 'Capricciosa',
          ingredients: ['Tomato sauce', 'Mozzarella', 'Ham', 'Artichoke', 'Mushrooms', 'Olives'],
          image: img('photo-1707528904286-f77b5d480d9f')
        },
        {
          name: 'Carnivora',
          ingredients: ['Tomato sauce', 'Mozzarella', 'Pepperoni', 'Sausage', 'Bacon', 'Parmesan'],
          image: img('photo-1705286324371-d6a6d9519dc2')
        }
      ]
    },
    {
      id: 'cream',
      label: 'Cream',
      title: 'Cream bases',
      note: 'Garlic cream and béchamel—comfort without the weight.',
      pizzas: [
        {
          name: 'Spinach ricotta',
          ingredients: ['Garlic cream', 'Ricotta', 'Spinach', 'Mozzarella', 'Lemon zest'],
          image: img('photo-1513104890138-7c749659a591')
        },
        {
          name: 'Truffle mushroom',
          ingredients: ['Cream sauce', 'Mixed mushrooms', 'Mozzarella', 'Parmesan', 'Truffle oil'],
          image: img('photo-1654722906292-1f712f25a799')
        },
        {
          name: 'Chicken alfredo',
          ingredients: ['Parmesan cream', 'Roasted chicken', 'Mozzarella', 'Black pepper', 'Parsley'],
          image: img('photo-1708989175809-150575781da5')
        },
        {
          name: 'Quattro formaggi',
          ingredients: ['White base', 'Mozzarella', 'Gorgonzola', 'Fontina', 'Parmesan'],
          image: img('photo-1593504049359-74330189a345')
        },
        {
          name: 'Pear & gorgonzola',
          ingredients: ['Crème fraîche base', 'Gorgonzola dolce', 'Pear', 'Walnuts', 'Honey'],
          image: img('photo-1760538635911-dee3b46f2011')
        },
        {
          name: 'Patate e rosmarino',
          ingredients: ['Garlic cream', 'Thin-sliced potato', 'Mozzarella', 'Rosemary', 'Sea salt'],
          image: img('photo-1615932736372-f3ce2c21db45')
        }
      ]
    },
    {
      id: 'pesto',
      label: 'Pesto',
      title: 'Pesto & herbs',
      note: 'Basil-forward bases with olive oil and punch.',
      pizzas: [
        {
          name: 'Pesto primavera',
          ingredients: ['Genovese pesto', 'Mozzarella', 'Cherry tomato', 'Courgette', 'Pine nuts'],
          image: img('photo-1571997478779-2adcbbe9ab2f')
        },
        {
          name: 'Prosciutto & rocket',
          ingredients: ['Pesto base', 'Mozzarella', 'Prosciutto crudo', 'Rocket', 'Parmesan'],
          image: img('photo-1528137871618-79d2761e3fd5')
        },
        {
          name: 'Bufala verde',
          ingredients: ['Pesto', 'Bufala', 'Cherry tomato', 'Basil leaves', 'EVO'],
          image: img('photo-1707528904229-d7e583579d1a')
        },
        {
          name: 'Mortadella & pistachio',
          ingredients: ['Pesto', 'Stracciatella', 'Mortadella', 'Pistachio', 'Lemon'],
          image: img('photo-1679310290400-53df391fae3c')
        },
        {
          name: 'Pesto calabrese',
          ingredients: ['Pesto', 'Mozzarella', 'Spicy salami', 'Roasted peppers', 'Pecorino'],
          image: img('photo-1705538150113-25b08da3ae07')
        }
      ]
    },
    {
      id: 'specialty',
      label: 'Specialty',
      title: 'Specialty sauces',
      note: 'House blends when you want a break from red or white.',
      pizzas: [
        {
          name: 'Smoky BBQ chicken',
          ingredients: ['BBQ sauce', 'Mozzarella', 'Chicken', 'Red onion', 'Corn', 'Cilantro'],
          image: img('photo-1705538150113-25b08da3ae07')
        },
        {
          name: 'Buffalo blue',
          ingredients: ['Buffalo sauce', 'Mozzarella', 'Chicken', 'Blue cheese', 'Celery'],
          image: img('photo-1773620497483-aafa93392160')
        },
        {
          name: 'Chipotle ranch',
          ingredients: ['Chipotle ranch', 'Mozzarella', 'Chicken', 'Sweetcorn', 'Crispy onion'],
          image: img('photo-1565299624946-b28f40a0ae38')
        },
        {
          name: 'Teriyaki chicken',
          ingredients: ['Teriyaki glaze', 'Mozzarella', 'Chicken', 'Sesame', 'Spring onion'],
          image: img('photo-1725119114900-71f5f72abed3')
        },
        {
          name: 'Detroit red-top',
          ingredients: ['Brick cheese edge', 'Tomato stripes', 'Pepperoni cup', 'Oregano', 'Butter crust'],
          image: img('photo-1758982126977-168e00f33230')
        }
      ]
    }
  ]

  /** House favourites — opens same modal as menu cards */
  const featuredPicks = [
    { price: '12,90 €', cat: categories[0], pizza: categories[0].pizzas[0] },
    { price: '14,50 €', cat: categories[1], pizza: categories[1].pizzas[1] },
    { price: '13,20 €', cat: categories[2], pizza: categories[2].pizzas[0] }
  ]

  let selectedCategory = 'all'

  /** @type {{ pizza: typeof categories[0]['pizzas'][0], categoryId: string } | null} */
  let modal = null

  /** @type {HTMLButtonElement | undefined} */
  let modalCloseBtn

  let navOpen = false

  const navLinks = [
    { href: '#story', key: 'story' },
    { href: '#menu', key: 'menu' },
    { href: '#gallery', key: 'gallery' },
    { href: '#reserve', key: 'reserve' },
    { href: '#reviews', key: 'reviews' },
    { href: '#hours', key: 'hours' },
    { href: '#find-us', key: 'findUs' }
  ]

  const translations = {
    fr: {
      nav: {
        story: 'Histoire',
        explore: 'Explorer',
        menu: 'Menu',
        gallery: 'Galerie',
        reserve: 'Réserver',
        reviews: 'Avis',
        hours: 'Horaires',
        findUs: 'Nous trouver',
        visit: 'Visiter'
      },
      hero: {
        eyebrow: 'Four à bois · Saint-Marcellin-en-Forez',
        title: 'Une pizza qui a le goût du vrai travail.',
        text: 'Pâte longue fermentation, sauces maison et vrai four à bois. Venez pour la croûte, restez pour l’ambiance.',
        ctaMenu: 'Voir le menu',
        ctaReserve: 'Réserver une table'
      },
      story: {
        eyebrow: 'À propos du restaurant',
        title: 'Bien plus qu’une pizzeria',
        text: 'Depuis l’ouverture, Chez Mario pizzeria défend une cuisine sincère et un accueil chaleureux. Nous choisissons bien nos produits, cuisons fort, et servons les pizzas que l’on aime retrouver en fin de semaine.',
        link: 'Voir le menu'
      },
      explore: {
        eyebrow: 'Explorer',
        title: 'Par sauce et base',
        sub: 'Choisissez une base, on vous montre les pizzas qui vont avec.'
      },
      featured: {
        eyebrow: 'À la une',
        title: 'Sélection du moment',
        sub: 'Trois pizzas que les habitués reprennent encore et encore.'
      },
      features: { eyebrow: 'Pourquoi on revient', title: 'Une expérience qui marque' },
      menu: { label: 'Menu complet', allBases: 'Toutes les bases' },
      gallery: { eyebrow: 'Galerie', title: 'Des tables pour toutes les envies' },
      reserve: {
        eyebrow: 'Réservations',
        title: 'Réservez votre table ce soir',
        text: 'Dites-nous la date, l’heure et le nombre de personnes — on vous garde une place près du four.',
        call: 'Appeler pour réserver',
        email: 'Nous écrire'
      },
      reviews: { eyebrow: 'Avis', title: 'Ce que disent nos clients' },
      hours: { heading: 'Ouvert', tueSun: 'Mardi — Dimanche', monday: 'Lundi', closed: 'Fermé' },
      findUs: { eyebrow: 'Adresse', title: 'Nous trouver', cta: 'Ouvrir dans Google Maps' },
      footer: { visit: 'Visiter', tonight: 'Ce soir ?', reserve: 'Réserver une table', browse: 'Voir le menu' },
      modal: { ingredients: 'Ingrédients', close: 'Fermer' },
      ui: { menuTitle: 'Menu', lang: 'Langues', detailsFor: 'Voir les détails de' }
    },
    en: {
      nav: { story: 'Story', explore: 'Explore', menu: 'Menu', gallery: 'Gallery', reserve: 'Reserve', reviews: 'Reviews', hours: 'Hours', findUs: 'Find us', visit: 'Visit' },
      hero: { eyebrow: 'Wood-fired · Saint-Marcellin-en-Forez', title: 'Pizza that tastes like the effort behind it.', text: 'Long-ferment dough, sauces from scratch, and a real oven — not a conveyor belt. Come for the crust, stay for the room.', ctaMenu: 'View the menu', ctaReserve: 'Book a table' },
      story: { eyebrow: 'About the restaurant', title: 'More than just a pizzeria', text: 'Since opening day, Chez Mario pizzeria has been about honest food and warm hospitality. We source carefully, bake hot, and serve the kind of meal you crave after a long week.', link: 'See the menu' },
      explore: { eyebrow: 'Explore', title: 'By sauce & base', sub: 'Choose a lane — we’ll show the pizzas that match.' },
      featured: { eyebrow: 'Highlights', title: 'Selected for you', sub: 'Three plates the regulars order again and again.' },
      features: { eyebrow: 'Why guests return', title: 'Crafting memorable meals' },
      menu: { label: 'Full menu', allBases: 'All bases' },
      gallery: { eyebrow: 'Gallery', title: 'A table for every craving' },
      reserve: { eyebrow: 'Reservations', title: 'Book your table tonight', text: 'Tell us the date, time, and party size — we’ll hold a spot by the oven for you.', call: 'Call to reserve', email: 'Email us' },
      reviews: { eyebrow: 'Reviews', title: 'From our guests' },
      hours: { heading: 'Open', tueSun: 'Tuesday — Sunday', monday: 'Monday', closed: 'Closed' },
      findUs: { eyebrow: 'Location', title: 'Find us', cta: 'Open in Google Maps' },
      footer: { visit: 'Visit', tonight: 'Tonight?', reserve: 'Reserve a table', browse: 'Browse menu' },
      modal: { ingredients: 'Ingredients', close: 'Close' },
      ui: { menuTitle: 'Menu', lang: 'Languages', detailsFor: 'Open details for' }
    },
    it: {
      nav: { story: 'Storia', explore: 'Esplora', menu: 'Menu', gallery: 'Galleria', reserve: 'Prenota', reviews: 'Recensioni', hours: 'Orari', findUs: 'Dove siamo', visit: 'Visita' },
      hero: { eyebrow: 'Forno a legna · Saint-Marcellin-en-Forez', title: 'Pizza che sa di vero lavoro artigianale.', text: 'Impasto a lunga lievitazione, salse fatte in casa e forno vero. Vieni per la crosta, resta per l’atmosfera.', ctaMenu: 'Vedi il menu', ctaReserve: 'Prenota un tavolo' },
      story: { eyebrow: 'Il ristorante', title: 'Molto più di una pizzeria', text: 'Dal primo giorno, Chez Mario pizzeria significa cucina sincera e accoglienza calda. Scegliamo ingredienti con cura, cottura viva e pizze che vuoi ritrovare ogni settimana.', link: 'Scopri il menu' },
      explore: { eyebrow: 'Esplora', title: 'Per salsa e base', sub: 'Scegli una base e ti mostriamo le pizze abbinate.' },
      featured: { eyebrow: 'In evidenza', title: 'Scelte per te', sub: 'Tre pizze che i clienti ordinano sempre.' },
      features: { eyebrow: 'Perché tornano', title: 'Esperienze da ricordare' },
      menu: { label: 'Menu completo', allBases: 'Tutte le basi' },
      gallery: { eyebrow: 'Galleria', title: 'Un tavolo per ogni voglia' },
      reserve: { eyebrow: 'Prenotazioni', title: 'Prenota il tuo tavolo stasera', text: 'Dicci data, ora e numero di persone — ti teniamo un posto vicino al forno.', call: 'Chiama per prenotare', email: 'Scrivici' },
      reviews: { eyebrow: 'Recensioni', title: 'Parola ai nostri ospiti' },
      hours: { heading: 'Aperti', tueSun: 'Martedì — Domenica', monday: 'Lunedì', closed: 'Chiuso' },
      findUs: { eyebrow: 'Indirizzo', title: 'Dove siamo', cta: 'Apri su Google Maps' },
      footer: { visit: 'Visita', tonight: 'Stasera?', reserve: 'Prenota un tavolo', browse: 'Sfoglia il menu' },
      modal: { ingredients: 'Ingredienti', close: 'Chiudi' },
      ui: { menuTitle: 'Menu', lang: 'Lingue', detailsFor: 'Apri dettagli per' }
    }
  }

  const categoryI18n = {
    fr: {
      tomato: { label: 'Tomate', title: 'Bases tomate', note: 'Passata mijotée, acidité vive, accords classiques.' },
      cream: { label: 'Crème', title: 'Bases crème', note: 'Crème à l’ail et béchamel — généreux sans lourdeur.' },
      pesto: { label: 'Pesto', title: 'Pesto & herbes', note: 'Bases au basilic, huile d’olive et belle intensité.' },
      specialty: { label: 'Spéciales', title: 'Sauces spéciales', note: 'Nos sauces maison quand vous voulez sortir des classiques.' }
    },
    en: {
      tomato: { label: 'Tomato', title: 'Tomato bases', note: 'Slow-cooked passata, bright acidity, classic pairings.' },
      cream: { label: 'Cream', title: 'Cream bases', note: 'Garlic cream and béchamel — comfort without the weight.' },
      pesto: { label: 'Pesto', title: 'Pesto & herbs', note: 'Basil-forward bases with olive oil and punch.' },
      specialty: { label: 'Specialty', title: 'Specialty sauces', note: 'House blends when you want a break from red or white.' }
    },
    it: {
      tomato: { label: 'Pomodoro', title: 'Basi al pomodoro', note: 'Passata cotta lentamente, acidità viva, abbinamenti classici.' },
      cream: { label: 'Crema', title: 'Basi cremose', note: 'Crema all’aglio e besciamella — golosa ma equilibrata.' },
      pesto: { label: 'Pesto', title: 'Pesto & erbe', note: 'Basi al basilico con olio d’oliva e carattere deciso.' },
      specialty: { label: 'Speciali', title: 'Salse speciali', note: 'Le nostre salse di casa per uscire dai soliti gusti.' }
    }
  }

  const featuresByLocale = {
    fr: [
      { title: 'Saveurs authentiques', text: 'Four à bois, pâte longue fermentation et sauces maison — sans raccourcis.', glyph: '🔥' },
      { title: 'Produits frais', text: 'Légumes de saison, fromages de qualité et huile d’olive choisie avec soin.', glyph: '🌿' },
      { title: 'Accueil chaleureux', text: 'Ambiance de quartier, service attentionné et tables où l’on prend son temps.', glyph: '❤️' }
    ],
    en: [
      { title: 'Authentic flavours', text: 'Wood oven, long ferment dough, and sauces built from scratch — never shortcuts.', glyph: '🔥' },
      { title: 'Fresh ingredients', text: 'Seasonal produce, quality cheese, and the kind of olive oil we use at home.', glyph: '🌿' },
      { title: 'Warm service', text: 'Neighbourhood energy: unhurried tables, generous pours, and staff who remember you.', glyph: '❤️' }
    ],
    it: [
      { title: 'Sapori autentici', text: 'Forno a legna, impasto a lunga lievitazione e salse fatte in casa — senza scorciatoie.', glyph: '🔥' },
      { title: 'Ingredienti freschi', text: 'Prodotti stagionali, ottimi formaggi e olio d’oliva scelto con cura.', glyph: '🌿' },
      { title: 'Servizio caloroso', text: 'Atmosfera di quartiere, tempi rilassati e uno staff che si ricorda di te.', glyph: '❤️' }
    ]
  }

  const reviewsByLocale = {
    fr: [
      { quote: 'Chaque pizza débordait de saveur — croûte saisie, fromage filant et basilic ultra parfumé.', name: 'Claire M.' },
      { quote: 'Salle cosy, équipe souriante, portions généreuses. On se sent comme à la maison.', name: 'Thomas R.' },
      { quote: 'On revient pour les bases crème. Riches mais jamais lourdes — un vrai équilibre.', name: 'Amélie D.' }
    ],
    en: [
      { quote: 'Every pizza was bursting with flavour — blistered crust, perfect cheese pull, and basil that smelled like summer.', name: 'Claire M.' },
      { quote: 'Cosy room, friendly team, and portions that feel like Sunday dinner at nonna’s house.', name: 'Thomas R.' },
      { quote: 'We keep coming back for the cream bases. Luxurious without being heavy — rare magic.', name: 'Amélie D.' }
    ],
    it: [
      { quote: 'Ogni pizza era piena di sapore — bordo croccante, formaggio filante e basilico profumatissimo.', name: 'Claire M.' },
      { quote: 'Locale accogliente, team gentilissimo e porzioni generose come a casa.', name: 'Thomas R.' },
      { quote: 'Torniamo sempre per le basi crema: ricche ma mai pesanti, davvero speciali.', name: 'Amélie D.' }
    ]
  }

  $: i18n = translations[locale]
  $: features = featuresByLocale[locale]
  $: reviews = reviewsByLocale[locale]

  $: visibleCategories =
    selectedCategory === 'all'
      ? categories
      : categories.filter((category) => category.id === selectedCategory)

  $: if (typeof document !== 'undefined') {
    document.body.style.overflow = modal || navOpen ? 'hidden' : ''
  }

  function closeNav() {
    navOpen = false
  }

  function toggleNav() {
    navOpen = !navOpen
  }

  async function openModal(
    /** @type {typeof categories[0]} */ cat,
    /** @type {typeof categories[0]['pizzas'][0]} */ pizza
  ) {
    closeNav()
    modal = { pizza, categoryId: cat.id }
    await tick()
    modalCloseBtn?.focus()
  }

  function closeModal() {
    modal = null
  }

  onDestroy(() => {
    if (typeof document !== 'undefined') {
      document.body.style.overflow = ''
    }
  })

  const galleryImages = [
    img('photo-1604382355076-af4b0eb60143', 640),
    img('photo-1765629315623-ea6f41a8bada', 640),
    img('photo-1760538635911-dee3b46f2011', 640),
    img('photo-1758982126977-168e00f33230', 640),
    img('photo-1528137871618-79d2761e3fd5', 640),
    img('photo-1571407970349-bc81e7e96d47', 640)
  ]

  function categoryText(categoryId, key) {
    return categoryI18n[locale][categoryId][key]
  }

  /** @param {string} catId */
  async function selectCategoryAndScroll(catId) {
    selectedCategory = catId
    await tick()
    document.querySelector('#menu')?.scrollIntoView({ behavior: 'smooth', block: 'start' })
  }

  onMount(() => {
    function onDocumentKeydown(/** @type {KeyboardEvent} */ e) {
      if (e.key !== 'Escape') return
      if (modal) {
        closeModal()
        return
      }
      if (navOpen) closeNav()
    }

    window.addEventListener('keydown', onDocumentKeydown)

    return () => {
      window.removeEventListener('keydown', onDocumentKeydown)
    }
  })
</script>

<div class="shell">
  <header class="site-header" class:site-header--nav-open={navOpen}>
    <a class="site-header__brand" href="#top" aria-label={`${siteName} — home`} on:click={closeNav}>
      <img
        class="site-header__logo"
        src="/670619462_10244028958845929_2666723022924416195_n-removebg-preview.png"
        alt=""
        width="48"
        height="48"
      />
      <span class="site-header__title">{siteName}</span>
    </a>
    <div class="site-header__langs">
      <select id="lang-select" class="site-header__lang-select" bind:value={locale} aria-label={i18n.ui.lang}>
        {#each locales as lang}
          <option value={lang.id}>{lang.label}</option>
        {/each}
      </select>
    </div>
    <button
      type="button"
      class="site-header__burger"
      aria-expanded={navOpen}
      aria-controls="site-nav-drawer"
      aria-label={navOpen ? 'Close menu' : 'Open menu'}
      on:click={toggleNav}
    >
      <span class="site-header__burger-line" aria-hidden="true"></span>
      <span class="site-header__burger-line" aria-hidden="true"></span>
      <span class="site-header__burger-line" aria-hidden="true"></span>
    </button>
    <nav class="site-header__nav site-header__nav--desktop" aria-label="Page sections">
      {#each navLinks as item}
        <a class="site-header__link" href={item.href}>{i18n.nav[item.key]}</a>
      {/each}
      <a class="site-header__link site-header__link--cta" href="#contact">{i18n.nav.visit}</a>
    </nav>
  </header>

  <div
    id="site-nav-drawer"
    class="nav-drawer"
    class:nav-drawer--open={navOpen}
    inert={!navOpen}
    role="dialog"
    aria-modal="true"
    aria-hidden={!navOpen}
    aria-labelledby="nav-drawer-title"
  >
    <button type="button" class="nav-drawer__backdrop" tabindex="-1" aria-label="Close menu" on:click={closeNav}></button>
    <div class="nav-drawer__panel">
      <div class="nav-drawer__head">
        <h2 id="nav-drawer-title" class="nav-drawer__title">{i18n.ui.menuTitle}</h2>
        <button type="button" class="nav-drawer__close" aria-label="Close menu" on:click={closeNav}>
          ×
        </button>
      </div>
      <nav class="nav-drawer__links" aria-label="Main navigation">
        {#each navLinks as item}
          <a class="nav-drawer__link" href={item.href} on:click={closeNav}>{i18n.nav[item.key]}</a>
        {/each}
        <a class="nav-drawer__link nav-drawer__link--cta" href="#contact" on:click={closeNav}>{i18n.nav.visit}</a>
      </nav>
    </div>
  </div>

  <section class="hero-region" id="top" aria-label="Introduction">
    <div class="hero-split">
      <div class="hero-split__grid">
        <div class="hero-split__copy">
          <p class="hero-split__eyebrow">{i18n.hero.eyebrow}</p>
          <h1 class="hero-split__headline">{i18n.hero.title}</h1>
          <p class="hero-split__lede">{i18n.hero.text}</p>
          <div class="hero-split__cta-row">
            <a class="btn btn--primary" href="#menu">{i18n.hero.ctaMenu}</a>
            <a class="btn btn--ghost" href="#reserve">{i18n.hero.ctaReserve}</a>
          </div>
        </div>
        <figure class="hero-split__figure">
          <img
            class="hero-split__img"
            src={heroImageUrl}
            alt={heroImageAlt}
            width="1400"
            height="1050"
            fetchpriority="high"
          />
        </figure>
      </div>
    </div>
  </section>

  <section class="band band--paper" id="story" aria-labelledby="story-heading">
    <div class="band__inner band__inner--split">
      <div class="story__col story__col--text">
        <p class="kofeo-eyebrow">{i18n.story.eyebrow}</p>
        <h2 class="story__heading" id="story-heading">{i18n.story.title}</h2>
        <p class="story__text">{i18n.story.text}</p>
        <a class="story__link" href="#menu">{i18n.story.link}</a>
      </div>
      <div class="story__col story__col--visual" aria-hidden="true">
        <div class="story__visual-collage">
          <div class="story__visual-frame story__visual-frame--chef">
            <img
              src="/chef.jpg"
              alt=""
              width="520"
              height="700"
              loading="lazy"
            />
          </div>
          <div class="story__visual-stack">
            <div class="story__visual-frame story__visual-frame--main">
              <img
                src="/pizzayolo.jpg"
                alt=""
                width="640"
                height="800"
                loading="lazy"
              />
            </div>
            <div class="story__visual-frame story__visual-frame--accent">
              <img
                src="/pizza.jpg"
                alt=""
                width="560"
                height="420"
                loading="lazy"
              />
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>

  <section class="explore" id="explore" aria-labelledby="explore-heading">
    <p class="kofeo-eyebrow explore__eyebrow">{i18n.explore.eyebrow}</p>
    <h2 class="explore__title" id="explore-heading">{i18n.explore.title}</h2>
    <p class="explore__sub">{i18n.explore.sub}</p>
    <ul class="explore__grid">
      {#each categories as cat}
        <li class="explore__cell">
          <button
            type="button"
            class="explore-card"
            on:click={() => selectCategoryAndScroll(cat.id)}
          >
            <div class="explore-card__media">
              <img src={cat.pizzas[0].image} alt="" width="640" height="420" loading="lazy" />
            </div>
            <div class="explore-card__body">
              <span class="explore-card__eyebrow">{categoryText(cat.id, 'label')}</span>
              <h3 class="explore-card__name">{categoryText(cat.id, 'title')}</h3>
              <p class="explore-card__note">{categoryText(cat.id, 'note')}</p>
            </div>
          </button>
        </li>
      {/each}
    </ul>
  </section>

  <section class="band band--dark" id="featured" aria-labelledby="featured-heading">
    <div class="band__inner">
      <p class="kofeo-eyebrow featured__eyebrow">{i18n.featured.eyebrow}</p>
      <h2 class="featured__heading" id="featured-heading">{i18n.featured.title}</h2>
      <p class="featured__sub">{i18n.featured.sub}</p>
      <ul class="featured__grid">
        {#each featuredPicks as row}
          <li class="featured__item">
            <button
              type="button"
              class="featured__card"
              aria-label={`Open ${row.pizza.name}`}
              on:click={() => openModal(row.cat, row.pizza)}
            >
              <div class="featured__media">
                <img src={row.pizza.image} alt="" width="480" height="320" loading="lazy" />
              </div>
              <div class="featured__body">
                <h3 class="featured__name">{row.pizza.name}</h3>
                <p class="featured__tag">{categoryText(row.cat.id, 'title')}</p>
                <p class="featured__price">{row.price}</p>
              </div>
            </button>
          </li>
        {/each}
      </ul>
    </div>
  </section>

  <section class="features" id="experience" aria-labelledby="features-heading">
    <p class="kofeo-eyebrow features__eyebrow">{i18n.features.eyebrow}</p>
    <h2 class="features__title" id="features-heading">{i18n.features.title}</h2>
    <ul class="features__grid">
      {#each features as f}
        <li class="features__item">
          <span class="features__glyph" aria-hidden="true">{f.glyph}</span>
          <h3 class="features__name">{f.title}</h3>
          <p class="features__text">{f.text}</p>
        </li>
      {/each}
    </ul>
  </section>

  <div class="menu-wrap" id="menu">
    <p class="menu-wrap__label">{i18n.menu.label}</p>
    <nav class="jumps" aria-label="Filter menu categories">
      <button
        class="jumps__link"
        class:is-active={selectedCategory === 'all'}
        type="button"
        on:click={() => {
          selectedCategory = 'all'
        }}
      >
        {i18n.menu.allBases}
      </button>
      {#each categories as c}
        <button
          class="jumps__link"
          class:is-active={selectedCategory === c.id}
          type="button"
          on:click={() => {
            selectedCategory = c.id
          }}
        >
          {categoryText(c.id, 'label')}
        </button>
      {/each}
    </nav>

    <main class="menu">
      {#each visibleCategories as cat}
        <section class="block" id={cat.id} aria-labelledby="h-{cat.id}">
          <header class="block__intro">
            <h2 class="block__title" id="h-{cat.id}">{categoryText(cat.id, 'title')}</h2>
            <p class="block__note">{categoryText(cat.id, 'note')}</p>
          </header>

          <ul class="tiles">
            {#each cat.pizzas as pizza}
              <li class="tile-wrap">
                <button
                  type="button"
                  class="tile"
                  aria-haspopup="dialog"
                  aria-label={`Open details for ${pizza.name}`}
                  on:click={() => openModal(cat, pizza)}
                >
                  <div class="tile__media">
                    <img
                      src={pizza.image}
                      alt=""
                      width="640"
                      height="400"
                      loading="lazy"
                      decoding="async"
                    />
                  </div>
                  <div class="tile__body">
                    <h3 class="tile__name">{pizza.name}</h3>
                    <p class="tile__ingredients">{pizza.ingredients.join(' · ')}</p>
                  </div>
                </button>
              </li>
            {/each}
          </ul>
        </section>
      {/each}
    </main>
  </div>

  <section class="gallery" id="gallery" aria-labelledby="gallery-heading">
    <div class="gallery__head">
      <p class="kofeo-eyebrow">{i18n.gallery.eyebrow}</p>
      <h2 class="gallery__title" id="gallery-heading">{i18n.gallery.title}</h2>
    </div>
    <ul class="gallery__grid">
      {#each galleryImages as src, i}
        <li class="gallery__cell">
          <img src={src} alt="" width="400" height="300" loading="lazy" class="gallery__img" />
        </li>
      {/each}
    </ul>
  </section>

  <section class="reserve" id="reserve" aria-labelledby="reserve-heading">
    <div class="reserve__inner">
      <p class="kofeo-eyebrow reserve__eyebrow">{i18n.reserve.eyebrow}</p>
      <h2 class="reserve__title" id="reserve-heading">{i18n.reserve.title}</h2>
      <p class="reserve__text">{i18n.reserve.text}</p>
      <div class="reserve__actions">
        <a class="btn btn--primary" href="tel:+33123456789">{i18n.reserve.call}</a>
        <a class="btn btn--ghost btn--on-dark" href="mailto:bonjour@chezmario.example">{i18n.reserve.email}</a>
      </div>
    </div>
  </section>

  <section class="reviews" id="reviews" aria-labelledby="reviews-heading">
    <p class="kofeo-eyebrow reviews__eyebrow">{i18n.reviews.eyebrow}</p>
    <h2 class="reviews__title" id="reviews-heading">{i18n.reviews.title}</h2>
    <ul class="reviews__grid">
      {#each reviews as r}
        <li class="reviews__card">
          <p class="reviews__quote">“{r.quote}”</p>
          <p class="reviews__name">{r.name}</p>
        </li>
      {/each}
    </ul>
  </section>

  <section class="band band--dark band--hours" id="hours" aria-labelledby="hours-heading">
    <div class="band__inner band__inner--hours">
      <h2 class="hours__heading" id="hours-heading">{i18n.hours.heading}</h2>
      <div class="hours__grid">
        <div class="hours__block">
          <p class="hours__label">{i18n.hours.tueSun}</p>
          <p class="hours__value">11:30 — 22:00</p>
        </div>
        <div class="hours__block">
          <p class="hours__label">{i18n.hours.monday}</p>
          <p class="hours__value">{i18n.hours.closed}</p>
        </div>
      </div>
    </div>
  </section>

  <section class="find-us" id="find-us" aria-labelledby="find-us-heading">
    <div class="find-us__intro">
      <p class="kofeo-eyebrow find-us__eyebrow">{i18n.findUs.eyebrow}</p>
      <h2 class="find-us__title" id="find-us-heading">{i18n.findUs.title}</h2>
      <address class="find-us__address">
        {venueStreet}<br />
        {venueCityLine}
      </address>
      <a
        class="btn btn--primary find-us__cta"
        href={mapsOpenUrl}
        target="_blank"
        rel="noopener noreferrer"
      >
        {i18n.findUs.cta}
      </a>
    </div>
    <div class="find-us__map">
      <iframe
        class="find-us__iframe"
        title={`Map showing ${siteName}`}
        src={mapsEmbedUrl}
        width="600"
        height="420"
        loading="lazy"
        allowfullscreen
        referrerpolicy="no-referrer-when-downgrade"
      ></iframe>
    </div>
  </section>

  <footer class="site-footer" id="contact">
    <div class="site-footer__grid">
      <div class="site-footer__col">
        <p class="site-footer__brand">{siteName}</p>
        <p class="site-footer__address">
          {venueStreet}<br />
          {venueCityLine}
        </p>
        <p class="site-footer__meta">
          <a href="tel:+33123456789">+33 1 23 45 67 89</a><br />
          <a href="mailto:bonjour@chezmario.example">bonjour@chezmario.example</a>
        </p>
      </div>
      <div class="site-footer__col">
        <p class="site-footer__label">{i18n.footer.visit}</p>
        <ul class="site-footer__links">
          <li><a href="#story">{i18n.nav.story}</a></li>
          <li><a href="#menu">{i18n.nav.menu}</a></li>
          <li><a href="#gallery">{i18n.nav.gallery}</a></li>
          <li><a href="#reserve">{i18n.nav.reserve}</a></li>
          <li><a href="#reviews">{i18n.nav.reviews}</a></li>
          <li><a href="#hours">{i18n.nav.hours}</a></li>
          <li><a href="#find-us">{i18n.nav.findUs}</a></li>
        </ul>
      </div>
      <div class="site-footer__col site-footer__col--cta">
        <p class="site-footer__label">{i18n.footer.tonight}</p>
        <a class="btn btn--primary btn--block" href="#reserve">{i18n.footer.reserve}</a>
        <a class="btn btn--ghost btn--on-dark btn--block" href="#menu">{i18n.footer.browse}</a>
      </div>
    </div>
    <p class="site-footer__copy">© {new Date().getFullYear()} {siteName}. All rights reserved.</p>
  </footer>
</div>

{#if modal}
  <div class="modal-root" role="presentation" on:click|self={closeModal}>
    <div class="modal" role="dialog" aria-modal="true" aria-labelledby="modal-title">
      <button
        bind:this={modalCloseBtn}
        type="button"
        class="modal__close"
        aria-label={i18n.modal.close}
        on:click={closeModal}
      >
        ×
      </button>
      <p class="modal__eyebrow">{categoryText(modal.categoryId, 'title')}</p>
      <div class="modal__media">
        <img
          src={modal.pizza.image}
          alt={`${modal.pizza.name} pizza`}
          width="900"
          height="560"
          loading="eager"
          decoding="async"
        />
      </div>
      <div class="modal__body">
        <h2 class="modal__title" id="modal-title">{modal.pizza.name}</h2>
        <p class="modal__label">{i18n.modal.ingredients}</p>
        <ul class="modal__list">
          {#each modal.pizza.ingredients as line}
            <li>{line}</li>
          {/each}
        </ul>
      </div>
    </div>
  </div>
{/if}
