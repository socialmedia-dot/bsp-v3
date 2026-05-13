<template>
  <div class="news-hub">
    <!-- Header -->
    <header class="news-header">
      <div class="header-inner">
        <div class="brand">
          <span class="brand-icon">🌐</span>
          <div>
            <h1 class="brand-title">UK Hong Kong News Hub</h1>
            <p class="brand-sub">Auto-curated news for Hongkongers in Britain</p>
          </div>
        </div>
        <div class="header-meta">
          <span class="live-badge">🔴 Live</span>
          <span class="update-time">Updated {{ lastUpdated }}</span>
        </div>
      </div>
    </header>

    <!-- Controls -->
    <div class="controls-bar">
      <div class="search-box">
        <span class="search-icon">🔍</span>
        <input
          v-model="searchQuery"
          type="text"
          placeholder="Search news..."
          class="search-input"
        />
      </div>
      <div class="filter-tags">
        <button
          v-for="cat in categories"
          :key="cat"
          class="tag-btn"
          :class="{ active: activeCategory === cat }"
          @click="activeCategory = cat"
        >
          {{ cat }}
        </button>
      </div>
    </div>

    <!-- Stats -->
    <div class="stats-bar">
      <div class="stat-item">
        <span class="stat-num">{{ filteredNews.length }}</span>
        <span class="stat-label">articles today</span>
      </div>
      <div class="stat-item">
        <span class="stat-num">{{ sources.length }}</span>
        <span class="stat-label">sources</span>
      </div>
      <div class="stat-item">
        <span class="stat-num">24</span>
        <span class="stat-label">hours tracked</span>
      </div>
    </div>

    <!-- News Grid -->
    <div class="news-grid">
      <article
        v-for="item in filteredNews"
        :key="item.id"
        class="news-card"
        :class="{ featured: item.featured }"
      >
        <div class="card-image" v-if="item.image">
          <img :src="item.image" :alt="item.title" />
          <span class="card-category">{{ item.category }}</span>
        </div>
        <div class="card-body">
          <div class="card-meta">
            <span class="card-source">{{ item.source }}</span>
            <span class="card-time">{{ item.time }}</span>
          </div>
          <h2 class="card-title">{{ item.title }}</h2>
          <p class="card-summary">{{ item.summary }}</p>
          <div class="card-footer">
            <div class="card-tags">
              <span v-for="tag in item.tags" :key="tag" class="card-tag">{{ tag }}</span>
            </div>
            <a :href="item.url" target="_blank" class="read-more">Read more →</a>
          </div>
        </div>
      </article>
    </div>

    <!-- Footer -->
    <footer class="news-footer">
      <p>Aggregated from BBC, The Guardian, Sky News, Hong Kong Watch, and more.</p>
      <p class="footer-note">This is a demo prototype. Auto-scraping pipeline not yet connected.</p>
    </footer>
  </div>
</template>

<script setup lang="ts">
const searchQuery = ref('')
const activeCategory = ref('All')
const lastUpdated = ref(new Date().toLocaleString('en-GB', { hour: '2-digit', minute: '2-digit' }))

const categories = ['All', 'Immigration', 'Politics', 'Life', 'Work', 'Education']

const sources = ['BBC News', 'The Guardian', 'Sky News', 'Hong Kong Watch', 'The Times']

const newsItems = ref([
  {
    id: 1,
    title: "New UK visa scheme sees 140,000 Hongkongers apply in first two years",
    summary: "The BN(O) visa route has attracted significant interest, with latest Home Office figures showing continued strong demand among Hong Kong passport holders seeking to relocate to Britain.",
    source: "BBC News",
    time: "2 hours ago",
    category: "Immigration",
    tags: ["BNO", "Visa", "Home Office"],
    image: "https://picsum.photos/seed/bno1/400/250",
    url: "#",
    featured: true,
  },
  {
    id: 2,
    title: "Hong Kong migrants in UK report challenges finding professional jobs",
    summary: "A new survey by a Hong Kong diaspora group reveals that nearly half of respondents faced difficulties securing roles matching their qualifications, with many citing lack of local experience.",
    source: "The Guardian",
    time: "4 hours ago",
    category: "Work",
    tags: ["Employment", "Survey", "Skills"],
    image: "https://picsum.photos/seed/work1/400/250",
    url: "#",
    featured: false,
  },
  {
    id: 3,
    title: "Manchester sees fastest growth in Hong Kong community outside London",
    summary: "City councils report a surge in school registrations from Hong Kong families, prompting new community support services and Cantonese-language resources.",
    source: "Sky News",
    time: "5 hours ago",
    category: "Life",
    tags: ["Manchester", "Community", "Schools"],
    image: "https://picsum.photos/seed/mcr1/400/250",
    url: "#",
    featured: false,
  },
  {
    id: 4,
    title: "UK MPs call for action on Hong Kong activists facing harassment",
    summary: "Cross-party parliamentarians have written to the Foreign Secretary urging stronger measures to protect pro-democracy activists living in Britain from alleged transnational repression.",
    source: "The Times",
    time: "7 hours ago",
    category: "Politics",
    tags: ["Activists", "MPs", "Foreign Office"],
    image: "https://picsum.photos/seed/pol1/400/250",
    url: "#",
    featured: true,
  },
  {
    id: 5,
    title: "Hong Kong students adapt to British university life post-pandemic",
    summary: "Universities report record numbers of Hong Kong students this academic year, with many institutions expanding support services including mental health counselling in Cantonese.",
    source: "The Guardian",
    time: "9 hours ago",
    category: "Education",
    tags: ["University", "Students", "Mental Health"],
    image: "https://picsum.photos/seed/uni1/400/250",
    url: "#",
    featured: false,
  },
  {
    id: 6,
    title: "Hong Kong Watch report: British firms cautious on China ties",
    summary: "The advocacy group highlights growing tension for UK businesses navigating relationships with both mainland China and the expanding Hong Kong diaspora market.",
    source: "Hong Kong Watch",
    time: "11 hours ago",
    category: "Politics",
    tags: ["Business", "China", "Report"],
    image: "https://picsum.photos/seed/biz1/400/250",
    url: "#",
    featured: false,
  },
  {
    id: 7,
    title: "Cantonese language classes see boom across UK cities",
    summary: "Community centres and churches report waiting lists for Cantonese lessons, driven by second-generation families and local British residents interested in Hong Kong culture.",
    source: "BBC News",
    time: "12 hours ago",
    category: "Life",
    tags: ["Cantonese", "Culture", "Community"],
    image: "https://picsum.photos/seed/lang1/400/250",
    url: "#",
    featured: false,
  },
  {
    id: 8,
    title: "UK government extends Hong Kong welcome programme funding",
    summary: "An additional £5 million has been allocated to local councils to support integration services for Hong Kong arrivals, including English classes and employment support.",
    source: "Sky News",
    time: "14 hours ago",
    category: "Immigration",
    tags: ["Funding", "Integration", "Government"],
    image: "https://picsum.photos/seed/fund1/400/250",
    url: "#",
    featured: false,
  },
])

const filteredNews = computed(() => {
  let items = newsItems.value
  if (activeCategory.value !== 'All') {
    items = items.filter(n => n.category === activeCategory.value)
  }
  if (searchQuery.value.trim()) {
    const q = searchQuery.value.toLowerCase()
    items = items.filter(n =>
      n.title.toLowerCase().includes(q) ||
      n.summary.toLowerCase().includes(q) ||
      n.tags.some(t => t.toLowerCase().includes(q))
    )
  }
  return items
})
</script>

<style scoped>
.news-hub {
  min-height: 100vh;
  background: #f8fafc;
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  color: #1e293b;
}

/* Header */
.news-header {
  background: linear-gradient(135deg, #1e293b 0%, #334155 100%);
  color: white;
  padding: 2rem;
}
.header-inner {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.brand {
  display: flex;
  align-items: center;
  gap: 1rem;
}
.brand-icon {
  font-size: 2.5rem;
}
.brand-title {
  font-size: 1.5rem;
  font-weight: 700;
  margin: 0;
}
.brand-sub {
  font-size: 0.9rem;
  opacity: 0.8;
  margin: 0.25rem 0 0;
}
.header-meta {
  text-align: right;
}
.live-badge {
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
  background: #ef4444;
  padding: 0.35rem 0.75rem;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: 600;
}
.update-time {
  display: block;
  font-size: 0.8rem;
  opacity: 0.7;
  margin-top: 0.35rem;
}

/* Controls */
.controls-bar {
  max-width: 1200px;
  margin: 0 auto;
  padding: 1.5rem 2rem;
  display: flex;
  gap: 1.5rem;
  flex-wrap: wrap;
  align-items: center;
}
.search-box {
  flex: 1;
  min-width: 280px;
  position: relative;
}
.search-icon {
  position: absolute;
  left: 0.875rem;
  top: 50%;
  transform: translateY(-50%);
  font-size: 1rem;
  opacity: 0.5;
}
.search-input {
  width: 100%;
  padding: 0.75rem 1rem 0.75rem 2.5rem;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  font-size: 0.95rem;
  font-family: inherit;
  background: white;
}
.search-input:focus {
  outline: none;
  border-color: #3b82f6;
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
}
.filter-tags {
  display: flex;
  gap: 0.5rem;
  flex-wrap: wrap;
}
.tag-btn {
  padding: 0.5rem 1rem;
  border-radius: 20px;
  border: 1px solid #e2e8f0;
  background: white;
  font-size: 0.85rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s;
  color: #64748b;
}
.tag-btn:hover {
  border-color: #3b82f6;
  color: #3b82f6;
}
.tag-btn.active {
  background: #3b82f6;
  color: white;
  border-color: #3b82f6;
}

/* Stats */
.stats-bar {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 2rem 1.5rem;
  display: flex;
  gap: 2rem;
}
.stat-item {
  display: flex;
  align-items: baseline;
  gap: 0.4rem;
}
.stat-num {
  font-size: 1.5rem;
  font-weight: 700;
  color: #1e293b;
}
.stat-label {
  font-size: 0.85rem;
  color: #94a3b8;
}

/* News Grid */
.news-grid {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 2rem 2rem;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1.5rem;
}
.news-card {
  background: white;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
  transition: transform 0.2s, box-shadow 0.2s;
  display: flex;
  flex-direction: column;
}
.news-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.1);
}
.news-card.featured {
  grid-column: span 2;
  flex-direction: row;
}
.card-image {
  position: relative;
  flex-shrink: 0;
}
.news-card.featured .card-image {
  width: 50%;
}
.news-card:not(.featured) .card-image {
  height: 180px;
}
.card-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
.card-category {
  position: absolute;
  top: 0.75rem;
  left: 0.75rem;
  background: rgba(30, 41, 59, 0.85);
  color: white;
  padding: 0.25rem 0.75rem;
  border-radius: 4px;
  font-size: 0.75rem;
  font-weight: 600;
}
.card-body {
  padding: 1.25rem;
  display: flex;
  flex-direction: column;
  flex: 1;
}
.card-meta {
  display: flex;
  gap: 0.75rem;
  font-size: 0.8rem;
  color: #94a3b8;
  margin-bottom: 0.75rem;
}
.card-source {
  font-weight: 600;
  color: #3b82f6;
}
.card-title {
  font-size: 1.05rem;
  font-weight: 700;
  line-height: 1.4;
  margin-bottom: 0.5rem;
  color: #1e293b;
}
.news-card.featured .card-title {
  font-size: 1.35rem;
}
.card-summary {
  font-size: 0.9rem;
  color: #64748b;
  line-height: 1.6;
  margin-bottom: 1rem;
  flex: 1;
}
.card-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 0.5rem;
}
.card-tags {
  display: flex;
  gap: 0.4rem;
  flex-wrap: wrap;
}
.card-tag {
  background: #f1f5f9;
  color: #64748b;
  padding: 0.2rem 0.6rem;
  border-radius: 4px;
  font-size: 0.75rem;
  font-weight: 500;
}
.read-more {
  color: #3b82f6;
  font-weight: 600;
  font-size: 0.9rem;
  text-decoration: none;
}
.read-more:hover {
  text-decoration: underline;
}

/* Footer */
.news-footer {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem;
  text-align: center;
  color: #94a3b8;
  font-size: 0.85rem;
  border-top: 1px solid #e2e8f0;
}
.footer-note {
  font-style: italic;
  margin-top: 0.5rem;
}

/* Responsive */
@media (max-width: 1024px) {
  .news-grid {
    grid-template-columns: repeat(2, 1fr);
  }
  .news-card.featured {
    grid-column: span 2;
  }
}
@media (max-width: 768px) {
  .header-inner {
    flex-direction: column;
    gap: 1rem;
    align-items: flex-start;
  }
  .news-grid {
    grid-template-columns: 1fr;
  }
  .news-card.featured {
    grid-column: span 1;
    flex-direction: column;
  }
  .news-card.featured .card-image {
    width: 100%;
    height: 180px;
  }
  .controls-bar {
    flex-direction: column;
    align-items: stretch;
  }
}
</style>
