 @app.route('/')                          ← Python syntax
  │
  ├─ Scaffold.route()                      sansio/scaffold.py:336
  │   └─ decorator(index_fn)              sansio/scaffold.py:360
  │       └─ add_url_rule('/', None, fn)  sansio/scaffold.py:362
  │
  ├─ setupmethod wrapper                   sansio/scaffold.py:42
  │   └─ _check_setup_finished()          sansio/app.py:410
  │
  ├─ App.add_url_rule()                    sansio/app.py:602
  │   ├─ _endpoint_from_view_func()       sansio/scaffold.py:701  → "index"
  │   ├─ methods resolved                 sansio/app.py:619,625   → {"GET","HEAD","OPTIONS"}
  │   ├─ Rule('/', methods=...)           sansio/app.py:647       ← werkzeug.routing.Rule
  │   ├─ url_map.add(rule_obj)            sansio/app.py:650       ← werkzeug.routing.Map
  │   └─ view_functions["index"] = fn     sansio/app.py:658
  │
  │   ── REQUEST ARRIVES ──
  │
  ├─ Flask.create_url_adapter()           app.py:509
  │   └─ url_map.bind_to_environ()        app.py:548              ← werkzeug MapAdapter
  │
  ├─ AppContext.match_request()           ctx.py:405
  │   └─ url_adapter.match()             ctx.py:410              ← werkzeug match
  │       → request.url_rule = Rule('/')
  │       → request.view_args = {}
  │
  └─ Flask.dispatch_request()             app.py:966
      ├─ view_functions[rule.endpoint]    app.py:990  → view_functions["index"] → index_fn
      ├─ ensure_sync(index_fn)            app.py:1065 → no-op for plain def
      └─ index_fn()                       ← your view function is called