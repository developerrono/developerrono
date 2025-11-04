import { useState, useEffect, ReactNode } from 'react';
import { Github, Twitter, Linkedin, Globe, Eye, Code2, Database, Shield, Palette, Zap, ExternalLink } from 'lucide-react';

// ===== TYPING EFFECT COMPONENT =====
interface TypingEffectProps {
  texts: string[];
  typingSpeed?: number;
  deletingSpeed?: number;
  pauseDuration?: number;
}

const TypingEffect = ({ 
  texts, 
  typingSpeed = 100, 
  deletingSpeed = 50, 
  pauseDuration = 2000 
}: TypingEffectProps) => {
  const [currentTextIndex, setCurrentTextIndex] = useState(0);
  const [currentText, setCurrentText] = useState('');
  const [isDeleting, setIsDeleting] = useState(false);

  useEffect(() => {
    const targetText = texts[currentTextIndex];
    
    const timeout = setTimeout(() => {
      if (!isDeleting) {
        if (currentText.length < targetText.length) {
          setCurrentText(targetText.slice(0, currentText.length + 1));
        } else {
          setTimeout(() => setIsDeleting(true), pauseDuration);
        }
      } else {
        if (currentText.length > 0) {
          setCurrentText(currentText.slice(0, -1));
        } else {
          setIsDeleting(false);
          setCurrentTextIndex((prev) => (prev + 1) % texts.length);
        }
      }
    }, isDeleting ? deletingSpeed : typingSpeed);

    return () => clearTimeout(timeout);
  }, [currentText, isDeleting, currentTextIndex, texts, typingSpeed, deletingSpeed, pauseDuration]);

  return (
    <span className="gradient-text font-bold">
      {currentText}
      <span className="animate-pulse">|</span>
    </span>
  );
};

// ===== STREAK CALENDAR COMPONENT =====
interface StreakDay {
  date: Date;
  count: number;
}

const StreakCalendar = () => {
  const [streaks, setStreaks] = useState<StreakDay[]>([]);
  const [currentStreak] = useState(42);

  useEffect(() => {
    const today = new Date();
    const streakData: StreakDay[] = [];
    
    for (let i = 83; i >= 0; i--) {
      const date = new Date(today);
      date.setDate(date.getDate() - i);
      const count = Math.floor(Math.random() * 10);
      streakData.push({ date, count });
    }
    
    setStreaks(streakData);
  }, []);

  const getColor = (count: number) => {
    if (count === 0) return 'bg-muted/30';
    if (count < 3) return 'bg-primary/40';
    if (count < 6) return 'bg-primary/70';
    return 'bg-primary';
  };

  return (
    <div className="glass-card p-6 animate-slide-up">
      <div className="flex items-center justify-between mb-6">
        <h3 className="text-xl font-bold text-foreground">GitHub Contributions</h3>
        <div className="flex items-center gap-2">
          <span className="text-3xl animate-pulse">🔥</span>
          <div>
            <p className="text-2xl font-bold text-primary">{currentStreak}</p>
            <p className="text-xs text-muted-foreground">day streak</p>
          </div>
        </div>
      </div>
      
      <div className="grid grid-cols-12 gap-1">
        {streaks.map((day, index) => (
          <div
            key={index}
            className={`aspect-square rounded-sm ${getColor(day.count)} hover:ring-2 hover:ring-primary transition-all cursor-pointer ${
              day.count > 6 ? 'animate-streak' : ''
            }`}
            title={`${day.date.toDateString()}: ${day.count} contributions`}
          />
        ))}
      </div>
      
      <div className="flex items-center justify-end gap-2 mt-4 text-xs text-muted-foreground">
        <span>Less</span>
        <div className="flex gap-1">
          <div className="w-3 h-3 rounded-sm bg-muted/30" />
          <div className="w-3 h-3 rounded-sm bg-primary/40" />
          <div className="w-3 h-3 rounded-sm bg-primary/70" />
          <div className="w-3 h-3 rounded-sm bg-primary" />
        </div>
        <span>More</span>
      </div>
    </div>
  );
};

// ===== STATS CARD COMPONENT =====
interface StatsCardProps {
  icon: ReactNode;
  value: string | number;
  label: string;
  delay?: number;
}

const StatsCard = ({ icon, value, label, delay = 0 }: StatsCardProps) => {
  return (
    <div 
      className="glass-card p-6 hover:scale-105 transition-transform cursor-pointer group animate-slide-up"
      style={{ animationDelay: `${delay}ms` }}
    >
      <div className="flex items-center gap-4">
        <div className="text-4xl group-hover:scale-110 transition-transform">
          {icon}
        </div>
        <div>
          <p className="text-3xl font-bold text-primary group-hover:glow-text-primary transition-all">
            {value}
          </p>
          <p className="text-sm text-muted-foreground">{label}</p>
        </div>
      </div>
    </div>
  );
};

// ===== SOCIAL BADGE COMPONENT =====
interface SocialBadgeProps {
  icon: ReactNode;
  label: string;
  value: string;
  href: string;
}

const SocialBadge = ({ icon, label, value, href }: SocialBadgeProps) => {
  return (
    <a
      href={href}
      target="_blank"
      rel="noopener noreferrer"
      className="glass-card p-4 hover:scale-105 transition-all group flex items-center justify-between gap-4"
    >
      <div className="flex items-center gap-3">
        <div className="text-2xl text-primary group-hover:scale-110 transition-transform">
          {icon}
        </div>
        <div>
          <p className="text-xs text-muted-foreground">{label}</p>
          <p className="font-semibold text-foreground">{value}</p>
        </div>
      </div>
      <ExternalLink className="w-4 h-4 text-muted-foreground group-hover:text-primary transition-colors" />
    </a>
  );
};

// ===== TECH STACK COMPONENT =====
const techCategories = [
  {
    title: 'Frontend',
    icon: <Code2 className="w-5 h-5" />,
    techs: ['React', 'TypeScript', 'Tailwind CSS', 'Next.js'],
    color: 'text-primary'
  },
  {
    title: 'Backend',
    icon: <Database className="w-5 h-5" />,
    techs: ['Node.js', 'Express', 'MongoDB', 'PostgreSQL'],
    color: 'text-secondary'
  },
  {
    title: 'Security',
    icon: <Shield className="w-5 h-5" />,
    techs: ['JWT', 'OAuth', 'Encryption', 'OWASP'],
    color: 'text-success'
  },
  {
    title: 'Tools',
    icon: <Zap className="w-5 h-5" />,
    techs: ['Git', 'Docker', 'VS Code', 'Postman'],
    color: 'text-accent'
  }
];

const TechStack = () => {
  return (
    <div className="glass-card p-6 animate-slide-up">
      <h3 className="text-xl font-bold text-foreground mb-6 flex items-center gap-2">
        <Palette className="w-6 h-6 text-primary" />
        Tech Stack & Tools
      </h3>
      
      <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
        {techCategories.map((category, index) => (
          <div
            key={index}
            className="p-4 rounded-lg bg-muted/30 hover:bg-muted/50 transition-all group"
          >
            <div className="flex items-center gap-2 mb-3">
              <span className={`${category.color} group-hover:scale-110 transition-transform`}>
                {category.icon}
              </span>
              <h4 className="font-semibold text-foreground">{category.title}</h4>
            </div>
            <div className="flex flex-wrap gap-2">
              {category.techs.map((tech, techIndex) => (
                <span
                  key={techIndex}
                  className="px-3 py-1 text-xs rounded-full bg-background/50 text-foreground border border-border hover:border-primary transition-colors"
                >
                  {tech}
                </span>
              ))}
            </div>
          </div>
        ))}
      </div>
    </div>
  );
};

// ===== VISITOR COUNTER COMPONENT =====
const VisitorCounter = () => {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const target = 12847;
    const duration = 2000;
    const steps = 60;
    const increment = target / steps;
    let current = 0;

    const interval = setInterval(() => {
      current += increment;
      if (current >= target) {
        setCount(target);
        clearInterval(interval);
      } else {
        setCount(Math.floor(current));
      }
    }, duration / steps);

    return () => clearInterval(interval);
  }, []);

  return (
    <div className="glass-card px-6 py-3 inline-flex items-center gap-3">
      <Eye className="w-5 h-5 text-primary animate-pulse" />
      <div>
        <p className="text-xs text-muted-foreground">Total Views</p>
        <p className="text-2xl font-bold text-primary font-mono">{count.toLocaleString()}</p>
      </div>
    </div>
  );
};

// ===== MAIN INDEX PAGE =====
const Index = () => {
  const typingTexts = [
    'Full Stack Developer',
    'Cybersecurity Learner',
    'Open Source Builder',
    'Building with Purpose'
  ];

  return (
    <div className="min-h-screen py-12 px-4 sm:px-6 lg:px-8">
      <div className="max-w-7xl mx-auto space-y-8">
        {/* Hero Section */}
        <header className="text-center space-y-6 animate-slide-up">
          <div className="inline-block">
            <h1 className="text-6xl sm:text-7xl font-bold mb-2">
              <span className="glow-text-primary">✨</span> Developer Rono{' '}
              <span className="glow-text-accent">✨</span>
            </h1>
            <p className="text-xl text-muted-foreground mb-4">The Full Stack Alchemist</p>
          </div>
          
          <div className="text-2xl sm:text-3xl h-10">
            <TypingEffect texts={typingTexts} />
          </div>

          <p className="text-lg text-muted-foreground max-w-2xl mx-auto">
            💻 Building Secure Futures, One Commit at a Time
          </p>

          <div className="flex justify-center">
            <VisitorCounter />
          </div>
        </header>

        {/* Social Badges Grid */}
        <section className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
          <SocialBadge
            icon={<Twitter className="w-6 h-6" />}
            label="X (Twitter)"
            value="@iamrono"
            href="https://twitter.com/iamrono"
          />
          <SocialBadge
            icon={<Github className="w-6 h-6" />}
            label="GitHub"
            value="developerrono"
            href="https://github.com/developerrono"
          />
          <SocialBadge
            icon={<Linkedin className="w-6 h-6" />}
            label="LinkedIn"
            value="Connect"
            href="https://linkedin.com/in/developerrono"
          />
          <SocialBadge
            icon={<Globe className="w-6 h-6" />}
            label="Portfolio"
            value="View Site"
            href="https://ronoporfolio.vercel.app/"
          />
        </section>

        {/* Stats Cards */}
        <section className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
          <StatsCard
            icon="💻"
            value="500+"
            label="Commits This Year"
            delay={100}
          />
          <StatsCard
            icon="⭐"
            value="1.2K"
            label="Total Contributions"
            delay={200}
          />
          <StatsCard
            icon="📦"
            value="42"
            label="Projects Built"
            delay={300}
          />
          <StatsCard
            icon="☕"
            value="∞"
            label="Coffee Consumed"
            delay={400}
          />
        </section>

        {/* Streak Calendar */}
        <section>
          <StreakCalendar />
        </section>

        {/* Tech Stack */}
        <section>
          <TechStack />
        </section>

        {/* Footer Quote */}
        <footer className="text-center py-8 animate-slide-up">
          <p className="text-lg text-muted-foreground italic">
            "Code is poetry written in logic, debugged with patience, and deployed with hope."
          </p>
          <p className="text-sm text-primary mt-2">- Developer Rono</p>
        </footer>
      </div>
    </div>
  );
};

export default Index;
