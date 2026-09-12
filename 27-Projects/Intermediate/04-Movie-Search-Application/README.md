# Project: Movie Search Application

**Difficulty:** 🟡 Intermediate
**Modules Needed:** 13-16 (adds debounce from Module 22 conceptually)
**Previous:** [03-Notes-Application](../03-Notes-Application/README.md)
**Next:** [05-Shopping-Cart](../05-Shopping-Cart/README.md)

---

## 1. Requirements

Ek movie-search app jo real-time (debounced) search-as-you-type provide kare, ek movie-database API (jaise OMDb API) use karke.

## 2. Features

- Debounced live search (no need to press a button)
- Movie poster, title, year display
- Loading/error/empty states
- Click for more details (modal)

## 3. Architecture

```
movie-search/
├── index.html
├── style.css
└── script.js
```

**Note:** OMDb API (omdbapi.com) free API-key provide karta hai — is project ke liye use karo (Module 23's "never hardcode production secrets" applies less strictly to free, rate-limited, public-data APIs, lekin production mein still backend-proxy best-practice hai).

## 4. Step-by-Step Build Process

**Step 1 — HTML:**
```html
<div id="app">
  <input type="text" id="searchInput" placeholder="Search for a movie...">
  <div id="status"></div>
  <div id="resultsGrid"></div>
</div>
```

**Step 2 — Debounced search (Module 22, chapter 03 — a canonical real-world use-case):**
```js
const API_KEY = "YOUR_OMDB_KEY";
const BASE_URL = "https://www.omdbapi.com/";

function debounce(fn, delay) {
  let timeoutId;
  return function (...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => fn.apply(this, args), delay);
  };
}

async function searchMovies(query) {
  const status = document.getElementById("status");
  const grid = document.getElementById("resultsGrid");

  if (!query.trim()) {
    grid.innerHTML = "";
    status.textContent = "";
    return;
  }

  status.textContent = "Searching...";

  try {
    const response = await fetch(`${BASE_URL}?apikey=${API_KEY}&s=${encodeURIComponent(query)}`);
    const data = await response.json();

    if (data.Response === "False") { // OMDb's API signals errors THIS way, not via HTTP status!
      status.textContent = data.Error || "No movies found";
      grid.innerHTML = "";
      return;
    }

    status.textContent = `Found ${data.totalResults} results`;
    renderMovies(data.Search);
  } catch (error) {
    status.textContent = "Something went wrong. Please try again.";
    console.error(error);
  }
}

const debouncedSearch = debounce(searchMovies, 400); // Wait 400ms after typing stops
```
**Important API-specific gotcha:** OMDb (like many APIs) returns HTTP 200 even for "no results found" — the actual error is signaled via `data.Response === "False"`. This connects directly to Module 16, chapter 02's lesson that `fetch()` resolving successfully doesn't mean the "business logic" succeeded — always check the actual response body's structure too.

**Step 3 — Rendering and interaction:**
```js
function renderMovies(movies) {
  const grid = document.getElementById("resultsGrid");
  grid.innerHTML = movies.map(movie => `
    <div class="movie-card" data-id="${movie.imdbID}">
      <img src="${movie.Poster !== "N/A" ? movie.Poster : "placeholder.jpg"}" alt="${movie.Title}">
      <h3>${movie.Title}</h3>
      <p>${movie.Year}</p>
    </div>
  `).join("");
}

async function showMovieDetails(imdbID) {
  const response = await fetch(`${BASE_URL}?apikey=${API_KEY}&i=${imdbID}&plot=full`);
  const movie = await response.json();
  alert(`${movie.Title} (${movie.Year})\n\n${movie.Plot}`); // Simplified — a real app would use a proper modal
}

document.getElementById("searchInput").addEventListener("input", (event) => {
  debouncedSearch(event.target.value);
});

document.getElementById("resultsGrid").addEventListener("click", (event) => {
  const card = event.target.closest(".movie-card");
  if (card) showMovieDetails(card.dataset.id);
});
```

## 5. Explanation

- Debouncing (Module 22, chapter 03) is **essential** here — without it, every keystroke would trigger a network request, wasting API-quota and creating race-conditions (results from an earlier, now-stale search arriving after a later one).
- Placeholder-image fallback (`movie.Poster !== "N/A"`) handles OMDb's convention of returning the literal string `"N/A"` when no poster exists.
- Event delegation (Module 12, chapter 02) on `resultsGrid` handles clicks on dynamically-rendered movie-cards.

## 6. Debugging

- Agar search kabhi results nahi deta: verify API-key valid hai (OMDb requires free registration), aur check `data.Response` field directly (not just `response.ok`).
- Agar rapid-typing bahut zyada requests trigger kare: verify debounce properly wired hai — `console.log` add karo `searchMovies` ke andar dekhne ke liye kitni baar actually call hoti hai.

## 7. Testing

Type quickly (should only trigger one search after you stop), search for a non-existent movie (should show "No movies found"), click a result to see details-modal/alert.

## 8. Improvements

- Add a proper modal (instead of `alert()`) with more details (cast, ratings, genre).
- Add pagination for large result-sets (Module 16, chapter 04's pagination concepts).
- Cache repeated searches (Module 22, chapter 04's caching pattern) to avoid redundant API calls.

## 9. Advanced Version

Add a "favorites" list (persisted via localStorage, Module 11 chapter 05) where users can save movies, with a separate view to browse saved favorites.

---

**Next:** [05-Shopping-Cart](../05-Shopping-Cart/README.md)
