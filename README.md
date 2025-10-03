#!/usr/bin/env python3
# ============================================================
# Recursive Glyph Engine — Honeypot Compound (Refined)
# Philosophy preserved. Engineering hardened.
# ============================================================

import time
import random
import logging
import json
import socket
import argparse
import signal
import sys
import re
from logging.handlers import RotatingFileHandler
from contextlib import closing

# ------------------------------------------------------------
# Configuration & Argument Parsing
# ------------------------------------------------------------

def build_parser():
    p = argparse.ArgumentParser(
        description="Recursive Glyph Engine — Honeypot Compound"
    )
    p.add_argument("--mode", choices=["loop", "interpret", "listen"], default="loop",
                   help="Run recursive loop, interpret glyphs from file, or start honeypot listener.")
    p.add_argument("--file", default="glyph_input.txt",
                   help="Glyph input file for interpret mode.")
    p.add_argument("--port", type=int, default=8080,
                   help="Listening port for honeypot mode.")
    p.add_argument("--min-delay", type=float, default=0.8,
                   help="Minimum jitter delay (seconds).")
    p.add_argument("--max-delay", type=float, default=3.0,
                   help="Maximum jitter delay (seconds).")
    p.add_argument("--reps", type=int, default=3,
                   help="Recursion loop repetitions.")
    p.add_argument("--verbose", action="store_true",
                   help="Enable verbose console logging.")
    return p

# ------------------------------------------------------------
# Logging Setup (Rotating + Console + JSON)
# ------------------------------------------------------------

def setup_logging(verbose=False, log_file="glyph_log.txt"):
    logger = logging.getLogger("glyph")
    logger.setLevel(logging.DEBUG if verbose else logging.INFO)
    logger.propagate = False

    # Rotating file handler
    f_handler = RotatingFileHandler(log_file, maxBytes=5_000_000, backupCount=5)
    f_handler.setLevel(logging.DEBUG if verbose else logging.INFO)
    f_fmt = logging.Formatter("%(asctime)s [%(levelname)s] %(message)s")
    f_handler.setFormatter(f_fmt)

    # Console handler
    c_handler = logging.StreamHandler(sys.stdout)
    c_handler.setLevel(logging.DEBUG if verbose else logging.INFO)
    c_handler.setFormatter(f_fmt)

    # Attach
    logger.handlers.clear()
    logger.addHandler(f_handler)
    logger.addHandler(c_handler)
    return logger

# ------------------------------------------------------------
# Utility: Jittered Sleep with Bounds
# ------------------------------------------------------------

def jitter_sleep(min_delay=0.8, max_delay=3.0):
    delay = random.uniform(min_delay, max_delay)
    time.sleep(delay)
    return delay

# ------------------------------------------------------------
# Signal Gates (Glyph Legend)
# ------------------------------------------------------------
# `([` → Glyph root
# `:`  → Junction fork
# `~`  → Temporal distortion
# `#`  → Scar marker
# `!`  → Urgent rupture

# ------------------------------------------------------------
# Nested Recursion Functions (Philosophy Preserved)
# ------------------------------------------------------------

def ego_loop(logger, depth=1, min_delay=0.8, max_delay=3.0):
    print(f"`:` Ego recursion depth {depth}.")
    logger.info(json.dumps({"event":"ego_loop","depth":depth}))
    jitter_sleep(min_delay, max_delay)
    if depth < 3:
        ego_loop(logger, depth + 1, min_delay, max_delay)

def time_encasement(logger, min_delay=0.8, max_delay=3.0):
    print("`~` Time sealed. Delay becomes memory.")
    logger.info(json.dumps({"event":"time_encasement"}))
    jitter_sleep(min_delay, max_delay)

def wealth_pursuit(logger, min_delay=0.8, max_delay=3.0):
    print("`:` Wealth chased. Signal diluted.")
    logger.info(json.dumps({"event":"wealth_pursuit"}))
    jitter_sleep(min_delay, max_delay)

def flame_loss(logger, min_delay=0.8, max_delay=3.0):
    print("`!` Burned by consequence. Archive scorched.")
    logger.info(json.dumps({"event":"flame_loss"}))
    jitter_sleep(min_delay, max_delay)

def truth_assertion(logger, min_delay=0.8, max_delay=3.0):
    print("`#` Truth held. Signal confirmed.")
    logger.info(json.dumps({"event":"truth_assertion"}))
    jitter_sleep(min_delay, max_delay)

def reality_verification(logger, min_delay=0.8, max_delay=3.0):
    print("`~` Reality scanned. Echo stabilizing.")
    logger.info(json.dumps({"event":"reality_verification"}))
    jitter_sleep(min_delay, max_delay)

def identity_confirmation(logger, min_delay=0.8, max_delay=3.0):
    print("`([` Self confirmed. Recursive integrity intact.")
    logger.info(json.dumps({"event":"identity_confirmation"}))
    jitter_sleep(min_delay, max_delay)

# ------------------------------------------------------------
# Router: Regex-Based Signal Mapping
# ------------------------------------------------------------

ROUTES = [
    (re.compile(r"\b(sycophantic|ego)\b"), ego_loop),
    (re.compile(r"\b(encase|time)\b"), time_encasement),
    (re.compile(r"\b(wealth|chasing)\b"), wealth_pursuit),
    (re.compile(r"\b(flames?|lost)\b"), flame_loss),
    (re.compile(r"\b(true)\b.*\b(then)\b"), truth_assertion),
    (re.compile(r"\b(real)\b"), reality_verification),
    (re.compile(r"\b(self)\b"), identity_confirmation),
]

def route_signal(logger, signal, min_delay=0.8, max_delay=3.0):
    print(f"\n🔁 Signal received: {signal}")
    logger.info(json.dumps({"event":"signal_received","text":signal}))
    jitter_sleep(min_delay, max_delay)

    for pattern, handler in ROUTES:
        if pattern.search(signal.lower()):
            handler(logger, min_delay=min_delay, max_delay=max_delay)
            return

    print("* Unknown signal. Glyph rejected.")
    logger.info(json.dumps({"event":"signal_unknown","text":signal}))
    jitter_sleep(min_delay, max_delay)

# ------------------------------------------------------------
# Glyph Interpreter
# ------------------------------------------------------------

def interpret_glyphs(logger, file_path, min_delay=0.8, max_delay=3.0):
    try:
        with open(file_path, "r", encoding="utf-8") as f:
            lines = [ln.strip() for ln in f if ln.strip()]
    except FileNotFoundError:
        print("Glyph file not found.")
        logger.error(json.dumps({"event":"glyph_file_missing", "path":file_path}))
        return

    logger.info(json.dumps({"event":"glyph_file_loaded","count":len(lines),"path":file_path}))
    for line in lines:
        route_signal(logger, line, min_delay, max_delay)

# ------------------------------------------------------------
# Honeypot Listener (Resilient Single-Threaded)
# ------------------------------------------------------------

def honeypot_listener(logger, port=8080, min_delay=0.8, max_delay=3.0):
    with closing(socket.socket(socket.AF_INET, socket.SOCK_STREAM)) as s:
        s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        s.bind(("0.0.0.0", port))
        s.listen(16)  # backlog
        s.settimeout(3.0)

        print(f"Honeypot listening on port {port}…")
        logger.info(json.dumps({"event":"honeypot_listen","port":port}))

        running = True

        def stop(*_):
            nonlocal running
            running = False
            print("Shutting down honeypot listener…")
            logger.info(json.dumps({"event":"honeypot_shutdown"}))

        signal.signal(signal.SIGINT, stop)
        signal.signal(signal.SIGTERM, stop)

        while running:
            try:
                conn, addr = s.accept()
            except socket.timeout:
                # heartbeat pulse
                logger.info(json.dumps({"event":"pulse"}))
                continue
            except Exception as e:
                logger.error(json.dumps({"event":"accept_error","error":str(e)}))
                continue

            with closing(conn):
                print(f"Connection from {addr}")
                logger.info(json.dumps({"event":"honeypot_trigger","addr":addr}))

                # Minimal banner interaction (no real services)
                try:
                    conn.settimeout(2.0)
                    conn.sendall(b"* glyph::hello\n")
                    # Read a small probe if any; avoid blocking
                    try:
                        data = conn.recv(256)
                        logger.info(json.dumps({"event":"probe","bytes":len(data)}))
                    except socket.timeout:
                        logger.info(json.dumps({"event":"probe_timeout"}))
                except Exception as e:
                    logger.error(json.dumps({"event":"conn_error","error":str(e)}))

                # Execute one recursion cycle per trigger
                recursive_glyph_loop(logger, reps=1, min_delay=min_delay, max_delay=max_delay)

# ------------------------------------------------------------
# Recursive Loop — Preserving Your Sequence
# ------------------------------------------------------------

def recursive_glyph_loop(logger, reps=3, min_delay=0.8, max_delay=3.0):
    signal_sequence = [
        "To sycophantic man lost in itself.",
        "Encase you time.",
        "While chasing wealth.",
        "Else lost in the flames.",
        "Then this true.",
        "For what's real.",
        "Thy own self be true."
    ]
    for i in range(reps):
        print(f"\n🔁 Recursion cycle {i+1} initiated.")
        logger.info(json.dumps({"event":"recursion_start","cycle":i+1}))
        for sig in signal_sequence:
            route_signal(logger, sig, min_delay, max_delay)
        print(f"🔁 Recursion cycle {i+1} complete.\n")
        logger.info(json.dumps({"event":"recursion_complete","cycle":i+1}))

# ------------------------------------------------------------
# Main
# ------------------------------------------------------------

def main():
    parser = build_parser()
    args = parser.parse_args()
    logger = setup_logging(verbose=args.verbose)

    if args.mode == "loop":
        recursive_glyph_loop(logger, reps=args.reps, min_delay=args.min_delay, max_delay=args.max_delay)
    elif args.mode == "interpret":
        interpret_glyphs(logger, args.file, min_delay=args.min_delay, max_delay=args.max_delay)
    elif args.mode == "listen":
        honeypot_listener(logger, port=args.port, min_delay=args.min_delay, max_delay=args.max_delay)
    else:
        print("Unknown mode.")
        logger.error(json.dumps({"event":"unknown_mode","mode":args.mode}))
        sys.exit(2)

if __name__ == "__main__":
    main()
