<!DOCTYPE html>
<html>
<head>
    <title>My Twitter</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <script src="https://unpkg.com/react@18/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { 
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #000 0%, #1a1a1a 100%); 
            color: #fff; height: 100vh; overflow: hidden;
        }
        .app { height: 100vh; display: flex; flex-direction: column; }
        .header { 
            height: 53px; background: #000; padding: 0 16px; display: flex; 
            align-items: center; border-bottom: 1px solid #2f3336; position: sticky; top: 0; z-index: 10;
        }
        .logo { font-size: 20px; font-weight: bold; }
        .tabs { 
            height: 50px; background: #000; display: flex; border-bottom: 1px solid #2f3336;
        }
        .tab { flex: 1; display: flex; align-items: center; justify-content: center; font-size: 14px; cursor: pointer; }
        .tab.active { border-bottom: 3px solid #1DA1F2; color: #1DA1F2; font-weight: 600; }
        .feed { flex: 1; overflow-y: auto; padding: 10px; padding-bottom: 80px; }
        .tweet { 
            display: flex; padding: 16px; border-bottom: 1px solid #2f3336; 
            cursor: pointer; transition: all 0.2s; border-radius: 12px; margin-bottom: 8px;
            background: rgba(255,255,255,0.02);
        }
        .tweet:active { background: rgba(29,161,242,0.1); transform: scale(0.98); }
        .avatar { 
            width: 48px; height: 48px; border-radius: 50%; margin-right: 12px; 
            background: linear-gradient(45deg, #1DA1F2, #17BF63);
        }
        .content { flex: 1; min-width: 0; }
        .header-row { display: flex; align-items: center; margin-bottom: 4px; flex-wrap: wrap; }
        .name { font-weight: 700; font-size: 15px; margin-right: 4px; }
        .handle { color: #71767b; font-size: 15px; margin-right: 4px; }
        .time { color: #71767b; font-size: 15px; }
        .text { font-size: 15px; line-height: 1.4; margin-bottom: 12px; word-break: break-word; }
        .media { width: 100%; height: 200px; object-fit: cover; border-radius: 20px; margin-bottom: 12px; }
        .actions { display: flex; justify-content: space-between; max-width: 425px; }
        .action { 
            display: flex; align-items: center; padding: 8px 12px; border-radius: 20px; 
            color: #657786; font-size: 13px; transition: all 0.2s; cursor: pointer;
        }
        .action:hover { background: rgba(255,255,255,0.1); color: #1DA1F2; }
        .fab { 
            position: fixed; right: 20px; bottom: 25px; width: 56px; height: 56px; 
            border-radius: 50%; background: linear-gradient(45deg, #1DA1F2, #0d8bd9); 
            display: flex; align-items: center; justify-content: center; 
            font-size: 24px; box-shadow: 0 8px 25px rgba(29,161,242,0.4);
            animation: pulse 2s infinite;
        }
        @keyframes pulse { 0%, 100% { transform: scale(1); } 50% { transform: scale(1.05); } }
        .loading { text-align: center; padding: 40px; color: #71767b; }
    </style>
</head>
<body>
    <div id="root"></div>
    <script type="text/babel">
        const { useState, useEffect, useCallback } = React;

        const tweetsData = [
            { id: 1, user: "Elon Musk", handle: "@elonmusk", text: "🚀 Twitter is now X! Free speech forever. #Freedom", likes: 125432, retweets: 45432, replies: 2345, image: "https://images.unsplash.com/photo-1677442136019-21780ecad995?w=400&h=250&fit=crop" },
            { id: 2, user: "Cristiano Ronaldo", handle: "@Cristiano", text: "Siuuuu! Never give up! 💪⚽ #MUFC #CR7", likes: 256789, retweets: 67890, replies: 4567, image: "https://images.unsplash.com/photo-1571019613454-1cb2f99b2d8b?w=400&h=250&fit=crop" },
            { id: 3, user: "NASA", handle: "@NASA", text: "James Webb Space Telescope captures most distant galaxy ever! 🌌 #JWST", likes: 89234, retweets: 34567, replies: 2345, image: "https://images.unsplash.com/photo-1446776811953-b23d57bd21aa?w=400&h=250&fit=crop" },
            { id: 4, user: "Barack Obama", handle: "@BarackObama", text: "Yes we can! Change starts with hope. 🇺🇸", likes: 76543, retweets: 28901, replies: 1987 },
            { id: 5, user: "Your Twitter", handle: "@MyTwitterClone", text: "Welcome to YOUR Twitter! No bans, full control! 🔥 Share your thoughts freely. #FreeSpeech", likes: 1234, retweets: 567, replies: 89, image: "https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=400&h=250&fit=crop" },
            { id: 6, user: "Bill Gates", handle: "@BillGates", text: "Investing in clean energy is investing in our future. 🌍💡", likes: 54321, retweets: 23456, replies: 1678 },
        ];

        const App = () => {
            const [tweets, setTweets] = useState([]);
            const [activeTab, setActiveTab] = useState('Home');
            const [loading, setLoading] = useState(true);

            useEffect(() => {
                setTimeout(() => {
                    setTweets(tweetsData);
                    setLoading(false);
                }, 1000);
            }, []);

            useEffect(() => {
                const interval = setInterval(() => {
                    const newTweet = {
                        id: Date.now(),
                        user: "LIVE",
                        handle: `@Live${Math.floor(Math.random()*100)}`,
                        text: `Breaking: New update ${new Date().toLocaleTimeString()}! 📱`,
                        likes: Math.floor(Math.random() * 5000),
                        retweets: Math.floor(Math.random() * 2000),
                        replies: Math.floor(Math.random() * 500)
                    };
                    setTweets(prev => [newTweet, ...prev.slice(0, 5)]);
                }, 15000);
                return () => clearInterval(interval);
            }, []);

            const likeTweet = useCallback((id) => {
                setTweets(prev => prev.map(tweet => 
                    tweet.id === id ? { ...tweet, likes: tweet.likes + (Math.random()>0.5 ? 1 : -1) } : tweet
                ));
            }, []);

            const postTweet = () => {
                const tweetText = prompt("Write your tweet:");
                if (tweetText) {
                    const newTweet = {
                        id: Date.now(),
                        user: "You",
                        handle: "@YourHandle",
                        text: tweetText,
                        likes: 0,
                        retweets: 0,
                        replies: 0
                    };
                    setTweets(prev => [newTweet, ...prev]);
                    alert("Tweet posted! ✅");
                }
            };

            if (loading) {
                return <div className="loading">Loading Twitter...</div>;
            }

            return (
                <div className="app">
                    <div className="header">
                        <div className="logo" style={{marginLeft: 'auto'}}>𝕏</div>
                    </div>
                    
                    <div className="tabs">
                        <div className={`tab ${activeTab === 'Home' ? 'active' : ''}`} onClick={() => setActiveTab('Home')}>🏠 Home</div>
                        <div className={`tab ${activeTab === 'Search' ? 'active' : ''}`} onClick={() => setActiveTab('Search')}>🔍 Search</div>
                        <div className={`tab ${activeTab === 'Notifications' ? 'active' : ''}`} onClick={() => setActiveTab('Notifications')}>🔔 Notifs</div>
                        <div className={`tab ${activeTab === 'Messages' ? 'active' : ''}`} onClick={() => setActiveTab('Messages')}>💬 Messages</div>
                    </div>

                    <div className="feed">
                        {tweets.map(tweet => (
                            <div key={tweet.id} className="tweet">
                                <div className="avatar" style={{backgroundImage: `url(${tweet.image || 'https://via.placeholder.com/48/1DA1F2/ffffff?text=U'})`, backgroundSize: 'cover'}}></div>
                                <div className="content">
                                    <div className="header-row">
                                        <span className="name">{tweet.user}</span>
                                        <span className="handle">{tweet.handle}</span>
                                        <span className="time">· {Math.floor(Math.random()*24)}h</span>
                                    </div>
                                    <div className="text">{tweet.text}</div>
                                    {tweet.image && <img src={tweet.image} className="media" />}
                                    <div className="actions">
                                        <div className="action" onClick={e => {e.stopPropagation(); alert('Reply to ' + tweet.user);}}>💬 {tweet.replies.toLocaleString()}</div>
                                        <div className="action" onClick={e => {e.stopPropagation(); alert('Retweet!');}}>🔄 {tweet.retweets.toLocaleString()}</div>
                                        <div className="action" onClick={e => {e.stopPropagation(); likeTweet(tweet.id);}}>❤️ {tweet.likes.toLocaleString()}</div>
                                        <div className="action" onClick={e => {e.stopPropagation(); alert('Share tweet');}}>📤</div>
                                    </div>
                                </div>
                            </div>
                        ))}
                    </div>

                    <div className="fab" onClick={postTweet} title="Post Tweet">✍️</div>
                </div>
            );
        };

        ReactDOM.render(<App />, document.getElementById('root'));
    </script>
</body>
</html>
