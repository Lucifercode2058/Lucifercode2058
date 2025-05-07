import { Link, useNavigate, useLocation } from "react-router-dom";
import { useState, useRef, useEffect } from "react";
import { Menu, X, ChevronDown, User } from "lucide-react";
import logo from "../assets/Sunira-Logo-01.png";

const Navbar = () => {
  const navigate = useNavigate();
  const location = useLocation();
  const [isOpen, setIsOpen] = useState(false);
  const [servicesDropdown, setServicesDropdown] = useState(false);
  const dropdownRef = useRef(null);
  const menuButtonRef = useRef(null);
  const mobileMenuRef = useRef(null);
  const timeoutRef = useRef(null);

  // Navigate to home
  const handleLogoClick = () => {
    navigate("/");
    setIsOpen(false);
    setServicesDropdown(false);
  };

  // Toggle mobile menu
  const toggleMenu = () => {
    setIsOpen((prev) => !prev);
    setServicesDropdown(false);
  };

  // Show dropdown with delay for hiding
  const showServicesDropdown = () => {
    if (timeoutRef.current) {
      clearTimeout(timeoutRef.current);
    }
    setServicesDropdown(true);
  };

  // Hide dropdown with delay
  const hideServicesDropdown = () => {
    timeoutRef.current = setTimeout(() => {
      setServicesDropdown(false);
    }, 300);
  };

  // Toggle services dropdown for mobile and keyboard
  const toggleServicesDropdown = () => {
    if (servicesDropdown) {
      setServicesDropdown(false);
    } else {
      showServicesDropdown();
    }
  };

  // Check if route is active
  const isActive = (path) => {
    if (path === "/") {
      return location.pathname === "/";
    }
    if (path === "services") {
      return services.some((service) => location.pathname.startsWith(service.path));
    }
    return location.pathname.startsWith(path);
  };

  // Close dropdown and mobile menu when clicking outside
  useEffect(() => {
    const handleClickOutside = (event) => {
      if (
        dropdownRef.current &&
        !dropdownRef.current.contains(event.target) &&
        mobileMenuRef.current &&
        !mobileMenuRef.current.contains(event.target) &&
        !menuButtonRef.current.contains(event.target)
      ) {
        setIsOpen(false);
        hideServicesDropdown();
      }
    };

    document.addEventListener("mousedown", handleClickOutside);
    return () => {
      document.removeEventListener("mousedown", handleClickOutside);
      if (timeoutRef.current) {
        clearTimeout(timeoutRef.current);
      }
    };
  }, []);

  // Handle Escape key to close menus
  useEffect(() => {
    const handleEscKey = (event) => {
      if (event.key === "Escape") {
        setIsOpen(false);
        setServicesDropdown(false);
        menuButtonRef.current?.focus();
      }
    };

    document.addEventListener("keydown", handleEscKey);
    return () => document.removeEventListener("keydown", handleEscKey);
  }, []);

  // Focus trap for mobile menu
  useEffect(() => {
    if (isOpen && mobileMenuRef.current) {
      const focusableElements = mobileMenuRef.current.querySelectorAll(
        'a[href], button, [tabindex]:not([tabindex="-1"])'
      );
      const firstElement = focusableElements[0];
      const lastElement = focusableElements[focusableElements.length - 1];

      const handleTabKey = (e) => {
        if (e.key === "Tab") {
          if (e.shiftKey && document.activeElement === firstElement) {
            e.preventDefault();
            lastElement.focus();
          } else if (!e.shiftKey && document.activeElement === lastElement) {
            e.preventDefault();
            firstElement.focus();
          }
        }
      };

      document.addEventListener("keydown", handleTabKey);
      firstElement?.focus();

      return () => document.removeEventListener("keydown", handleTabKey);
    }
  }, [isOpen]);

  const services = [
    { name: "Mediation", path: "/mediation" },
    { name: "Legal", path: "/legal" },
    { name: "Astrology", path: "/astrology" },
    { name: "Real Estate", path: "/realestate" },
    { name: "Land Services", path: "/landservice" },
  ];

  return (
    <header className="bg-gradient-to-r from-blue-600 via-purple-600 to-blue-600 text-white shadow-lg sticky top-0 z-50 animate-gradient">
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-3 sm:py-4">
        <nav className="flex justify-between items-center" aria-label="Main navigation">
          {/* Logo and Profile */}
          <div
            className="flex items-center space-x-3 cursor-pointer group"
            onClick={handleLogoClick}
            role="button"
            tabIndex={0}
            onKeyDown={(e) => e.key === "Enter" && handleLogoClick()}
            aria-label="Navigate to homepage"
          >
            <img
              className="h-12 sm:h-14 md:h-16 rounded-full border-2 border-white group-hover:scale-105 transition-transform duration-300"
              src={logo}
              alt="Sunira Logo"
            />
            <div className="hidden sm:block text-sm sm:text-base font-semibold tracking-wide group-hover:text-yellow-300 transition-colors duration-300">
              Rabin Subedi
            </div>
          </div>

          {/* Desktop Navigation */}
          <div className="hidden md:flex items-center space-x-4 lg:space-x-6">
            <Link
              to="/"
              className={`relative px-3 py-1 text-sm lg:text-base font-medium transition-all duration-300 ${
                isActive("/") ? "text-yellow-300" : "text-white hover:text-yellow-300 hover:scale-105"
              }`}
            >
              Home
              {isActive("/") && (
                <span className="absolute bottom-0 left-0 w-full h-0.5 bg-yellow-300 transition-transform duration-300 scale-x-100 origin-left"></span>
              )}
            </Link>
            <Link
              to="/aboutUs"
              className={`relative px-3 py-1 text-sm lg:text-base font-medium transition-all duration-300 ${
                isActive("/aboutUs") ? "text-yellow-300" : "text-white hover:text-yellow-300 hover:scale-105"
              }`}
            >
              About Us
              {isActive("/aboutUs") && (
                <span className="absolute bottom-0 left-0 w-full h-0.5 bg-yellow-300 transition-transform duration-300 scale-x-100 origin-left"></span>
              )}
            </Link>

            {/* Services Dropdown */}
            <div
              className="relative group"
              ref={dropdownRef}
              onMouseEnter={showServicesDropdown}
              onMouseLeave={hideServicesDropdown}
            >
              <button
                className={`flex items-center relative px-3 py-1 text-sm lg:text-base font-medium transition-all duration-300 ${
                  isActive("services") ? "text-yellow-300" : "text-white hover:text-yellow-300 hover:scale-105"
                } focus:outline-none focus:text-yellow-300`}
                aria-expanded={servicesDropdown}
                aria-haspopup="true"
                onClick={toggleServicesDropdown}
              >
                Services <ChevronDown size={14} className="ml-1" />
                {isActive("services") && (
                  <span className="absolute bottom-0 left-0 w-full h-0.5 bg-yellow-300 transition-transform duration-300 scale-x-100 origin-left"></span>
                )}
              </button>
              <div
                className={`absolute top-full left-0 w-48 mt-2 bg-white border border-gray-200 rounded-md shadow-xl z-20 transition-all duration-300 ease-in-out ${
                  servicesDropdown
                    ? "opacity-100 pointer-events-auto translate-y-0"
                    : "opacity-0 pointer-events-none -translate-y-2"
                }`}
              >
                {services.map((service) => (
                  <Link
                    key={service.path}
                    to={service.path}
                    className={`block px-4 py-2 text-black text-sm font-medium transition-colors duration-200 ${
                      isActive(service.path)
                        ? "bg-blue-100 text-blue-600"
                        : "hover:bg-blue-50 hover:text-blue-600"
                    }`}
                    onClick={() => {
                      setServicesDropdown(false);
                      setIsOpen(false);
                    }}
                  >
                    {service.name}
                  </Link>
                ))}
              </div>
            </div>

            <Link
              to="/blog"
              className={`relative px-3 py-1 text-sm lg:text-base font-medium transition-all duration-300 ${
                isActive("/blog") ? "text-yellow-300" : "text-white hover:text-yellow-300 hover:scale-105"
              }`}
            >
              Blog
              {isActive("/blog") && (
                <span className="absolute bottom-0 left-0 w-full h-0.5 bg-yellow-300 transition-transform duration-300 scale-x-100 origin-left"></span>
              )}
            </Link>
            <Link
              to="/team"
              className={`relative px-3 py-1 text-sm lg:text-base font-medium transition-all duration-300 ${
                isActive("/team") ? "text-yellow-300" : "text-white hover:text-yellow-300 hover:scale-105"
              }`}
            >
              Team
              {isActive("/team") && (
                <span className="absolute bottom-0 left-0 w-full h-0.5 bg-yellow-300 transition-transform duration-300 scale-x-100 origin-left"></span>
              )}
            </Link>
            <a
              href="/#contact"
              className={`relative px-3 py-1 text-sm lg:text-base font-medium transition-all duration-300 ${
                location.hash === "#contact" && location.pathname === "/"
                  ? "text-yellow-300"
                  : "text-white hover:text-yellow-300 hover:scale-105"
              }`}
            >
              Contact Us
              {location.hash === "#contact" && location.pathname === "/" && (
                <span className="absolute bottom-0 left-0 w-full h-0.5 bg-yellow-300 transition-transform duration-300 scale-x-100 origin-left"></span>
              )}
            </a>
          </div>

          {/* Mobile Menu Button */}
          <div className="md:hidden flex items-center">
            <button
              ref={menuButtonRef}
              className="text-white focus:outline-none focus:ring-2 focus:ring-yellow-300 rounded-md p-2 hover:bg-blue-700 transition-colors duration-200"
              onClick={toggleMenu}
              aria-expanded={isOpen}
              aria-label={isOpen ? "Close menu" : "Open menu"}
            >
              {isOpen ? <X size={24} /> : <Menu size={24} />}
            </button>
          </div>
        </nav>

        {/* Mobile Menu Backdrop */}
        {isOpen && (
          <div
            className="fixed inset-0 bg-white z-40 md:hidden transition-opacity duration-300"
            onClick={() => setIsOpen(false)}
            aria-hidden="true"
          ></div>
        )}

        {/* Mobile Menu */}
        <div
          ref={mobileMenuRef}
          className={`md:hidden absolute top-full left-0 right-0 mx-4 mt-2 bg-white shadow-2xl rounded-lg z-50 transition-all duration-300 ease-in-out ${
            isOpen ? "max-h-[80vh] opacity-100" : "max-h-0 opacity-0 pointer-events-none overflow-hidden"
          }`}
        >
          <div className="flex flex-col p-4">
            {/* Close Button at Top */}
            <div className="flex items-center justify-between mb-3">
              <div className="flex items-center space-x-2">
                <User size={20} className="text-blue-600" />
                <span className="text-sm font-semibold text-gray-800">Rabin Subedi</span>
              </div>
              <button
                className="text-gray-600 focus:outline-none focus:ring-2 focus:ring-blue-600 rounded-md p-2 hover:bg-gray-100 transition-colors duration-200"
                onClick={() => setIsOpen(false)}
                aria-label="Close mobile menu"
              >
                <X size={20} />
              </button>
            </div>

            <Link
              to="/"
              className={`py-2 px-3 rounded-lg text-sm font-medium transition-colors duration-200 ${
                isActive("/") ? "bg-blue-600 text-white" : "text-gray-800 hover:bg-blue-100"
              }`}
              onClick={() => setIsOpen(false)}
            >
              Home
            </Link>
            <Link
              to="/aboutUs"
              className={`py-2 px-3 rounded-lg text-sm font-medium transition-colors duration-200 ${
                isActive("/aboutUs") ? "bg-blue-600 text-white" : "text-gray-800 hover:bg-blue-100"
              }`}
              onClick={() => setIsOpen(false)}
            >
              About Us
            </Link>

            {/* Mobile Services Dropdown */}
            <div className="relative">
              <button
                className={`flex items-center w-full text-left py-2 px-3 rounded-lg text-sm font-medium transition-colors duration-200 ${
                  isActive("services") ? "bg-blue-600 text-white" : "text-gray-800 hover:bg-blue-100"
                }`}
                onClick={toggleServicesDropdown}
                aria-expanded={servicesDropdown}
                aria-haspopup="true"
              >
                Services{" "}
                <ChevronDown
                  size={14}
                  className={`ml-1 transition-transform duration-200 ${servicesDropdown ? "rotate-180" : ""}`}
                />
              </button>
              <div
                className={`pl-4 mt-1 flex flex-col space-y-1 bg-white transition-all duration-300 ease-in-out ${
                  servicesDropdown ? "max-h-60 opacity-100" : "max-h-0 opacity-0 overflow-hidden"
                }`}
              >
                {services.map((service) => (
                  <Link
                    key={service.path}
                    to={service.path}
                    className={`py-2 px-3 rounded-lg text-black text-sm font-medium transition-colors duration-200 ${
                      isActive(service.path) ? "bg-blue-600 text-white" : "hover:bg-blue-100"
                    }`}
                    onClick={() => {
                      setServicesDropdown(false);
                      setIsOpen(false);
                    }}
                  >
                    {service.name}
                  </Link>
                ))}
              </div>
            </div>

            <Link
              to="/blog"
              className={`py-2 px-3 rounded-lg text-sm font-medium transition-colors duration-200 ${
                isActive("/blog") ? "bg-blue-600 text-white" : "text-gray-800 hover:bg-blue-100"
              }`}
              onClick={() => setIsOpen(false)}
            >
              Blog
            </Link>
            <Link
              to="/team"
              className={`py-2 px-3 rounded-lg text-sm font-medium transition-colors duration-200 ${
                isActive("/team") ? "bg-blue-600 text-white" : "text-gray-800 hover:bg-blue-100"
              }`}
              onClick={() => setIsOpen(false)}
            >
              Team
            </Link>
            <a
              href="/#contact"
              className={`py-2 px-3 rounded-lg text-sm font-medium transition-colors duration-200 ${
                location.hash === "#contact" && location.pathname === "/"
                  ? "bg-blue-600 text-white"
                  : "text-gray-800 hover:bg-blue-100"
              }`}
              onClick={() => setIsOpen(false)}
            >
              Contact Us
            </a>
          </div>
        </div>
      </div>

      {/* Tailwind Animation for Gradient */}
      <style>
        {`
          @keyframes gradient {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
          }
          .animate-gradient {
            background-size: 200% 200%;
            animation: gradient 15s ease infinite;
          }
        `}
      </style>
    </header>
  );
};

export default Navbar;
